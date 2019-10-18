## CloudFormation Mapping and Conditionals: Making Your Templates More Universal
### Learn about infrastructure as code and the benefits of AWS CloudFormation.

### by Don Mills

_Update: When I originally wrote this article, CloudFormation was written in JSON only.  Thank goodness over the years YAML has replaced it as the primary way folks write their templates.  AWS also seems to have modified how the "Fn::Not" intrinsic function works since I created the samples.... In light of these facts, and since this post still seems to benefit people, I've gone back and added YAML versions of the examples and rewritten the section that used "Fn::Not" to use "Fn::If".  Enjoy!_


One of the biggest maxims in Cloud DevOps (and Cloud Architecture in general) is the concept of “infrastructure as code”. This translates into a programmatic way to instantiate and configure environments that is not only self-documenting, but easily repeatable. Codifying the elements of a cloud deployment allows a process to be developed to predictably create the infrastructure multiple times, while eliminating the chances of human error resulting in misconfiguration or failure.  

In Amazon Web Services the primary tool for accomplishing these goals is CloudFormation. CloudFormation allows the engineer to develop templates that can be used to create “stacks” of resources in AWS that are linked together.

So with that obligatory introduction out of the way, let's get into it.    

Parameters are the primary way that people build variability into their CloudFormation templates so that they can be reused for multiple environments. But what if you, as the engineer, want to make templates that anyone can use without knowing all the values to place into the parameter fields? Or perhaps for security purposes you need some configuration items to be different for each environment, but you don't want to give the end user running the CloudFormation template the option to change the values for those items.  

For these situations, CloudFormation provides two elements known as Mappings and Conditionals. Mappings allow you to create simple “Key:Value” dictionaries or hashes for use in your resource declarations. And Conditionals allow you to use some logic-based decisions in your resources to add or modify values.  

Let's work with an example scenario. Suppose you have a customer who is putting an application out into AWS. This application will have four separate environments: Development, Testing, QA, and Production. The customer wants to use the same CloudFormation template for all environments, but wants the process to be simple for any of their staff to be able to run the stack creation process. The customer also has the requirement of separate configuration items (subnets, security groups, etc.) for each environment, but does not want the deployment staff to have the ability to modify these values at stack creation time. In addition, the QA environment will need additional software installed during instance creation, and the Production environment cannot have an associated ssh key.  

First step, the stack creator will need a way to choose which of the four environments are being created. So we start with a single parameter containing four choices in a drop down box:  

___JSON___  
```
"Parameters" : {
            "EnvironmentValue" : { 
                        "AllowedValues" : [
                                                "Dev",
                                                "Test",
                                                "QA",
                                                "Prod"
                                          ],
                       "Default" : "Dev",
                       "Description" : "What environment is this?",
                       "Type" : "String"
                                 }
               }
```
___YAML___ 
```
Parameters: 
  EnvironmentValue: 
    AllowedValues:
      - "Dev"
      - "Test"
      - "QA"
      - "Prod"
    Default: "Dev"
    Description: "What environment is this?"
    Type: String
```



Now when the user runs the stack creation, they can choose what environment they are creating...but what to do with that information? Well, next we create a Mapping that lists all the environmental specific values:  

___JSON___ 
```
"Mappings" : {  "Environments" : {
    "Dev" : { 
      "ELB" : "App-Dev", 
      "SecurityGroup" : "sg-12345678",
      "Subnets" : "subnet-aaaaaaaa,subnet-bbbbbbbb,subnet-cccccccc", 
      "ASMAX" : "1", 
      "ASMIN" : "1"
    },
    "Test" : { 
      "ELB" : "App-Test", 
      "SecurityGroup" : "sg-12345678",
      "Subnets" : "subnet-dddddddd,subnet-eeeeeeee,subnet-ffffffff", 
      "ASMAX" : "1", 
      "ASMIN" : "1"
    },
    "QA" : { 
      "ELB" : "App-QA", 
      "SecurityGroup" : "sg-87654321", 
      "Subnets" : "subnet-11111111,subnet-22222222,subnet-33333333", 
      "ASMAX" : "6", 
      "ASMIN" : "3"
    },
    "Prod" : { 
      "ELB" : "App-Prod", 
      "SecurityGroup" : "sg-87654321", 
      "Subnets" : "subnet-44444444,subnet-55555555,subnet-66666666", 
      "ASMAX" : "9", 
      "ASMIN" : "6"
    }
  }
},
```
___YAML___  
```
Mappings: 
  Environments:
    Dev:  
      ELB: App-Dev 
      SecurityGroup: sg-12345678
      Subnets: 
        - subnet-aaaaaaaa
        - subnet-bbbbbbbb
        - subnet-cccccccc 
      ASMAX: 1 
      ASMIN: 1
    Test:
      ELB: App-Test 
      SecurityGroup: sg-12345678
      Subnets: 
        - subnet-dddddddd
        - subnet-eeeeeeee
        - subnet-ffffffff 
      ASMAX: 1 
      ASMIN: 1
    QA:  
      ELB: App-QA 
      SecurityGroup: sg-87654321 
      Subnets:
        - subnet-11111111
        - subnet-22222222
        - subnet-33333333
      ASMAX: 6 
      ASMIN: 3
    Prod:  
      ELB: App-Prod 
      SecurityGroup: sg-87654321 
      Subnets: 
        - subnet-44444444
        - subnet-55555555
        - subnet-66666666 
      ASMAX: 9 
      ASMIN: 6
```

And we follow that with two Conditional statements:  

___JSON___ 
```
"Conditions" : {
  "QANotify" : {"Fn::Equals" : [{"Ref" : "EnvironmentValue"}, "QA"]},
  "ProdNotify" : {"Fn::Equals" : [{"Ref" : "EnvironmentValue"}, "Prod"]}
},
```
___YAML___
```
Conditions:
  QANotify:
    'Fn::Equals':
      - !Ref "EnvironmentValue"
      - "QA"
  ProdNotify:
    'Fn::Equals':
      - !Ref "EnvironmentValue"
      - "Prod"  
```
  

These set two conditions based on the value chosen in the drop down box at stack creation time. If the value is “QA”, the “QANotify” conditional becomes true. And if the value is “Prod”, then “ProdNotify” conditional becomes true. Note that only one of these can be true at any given time.  

Now that we have created our Mappings and Conditionals, we can use them throughout the template to create environment specific settings. Let's walk through a few examples.  

To set the values for an Auto Scaling group:  

___JSON___ 
```
"AppAutoScalingGroup" : {
  "Type" : "AWS::AutoScaling::AutoScalingGroup",
    ...
    "LoadBalancerNames" : [{
      "Fn::FindInMap" : [
        "Environments", { "Ref" : "EnvironmentValue"}, "ELB"
        ]
    }],
    "MinSize" : {
      "Fn::FindInMap" : ["Environments", { "Ref" : "EnvironmentValue"}, "ASMIN"]
    },
    "MaxSize" : {
      "Fn::FindInMap" : ["Environments", { "Ref" : "EnvironmentValue"}, "ASMAX"]
    },
    "VPCZoneIdentifier" : [{
      "Fn::FindInMap" : ["Environments", { "Ref" : "EnvironmentValue"}, "Subnets"]
    }],
    ...
},
```
___YAML___  
```
AppAutoScalingGroup: 
  Type: AWS::AutoScaling::AutoScalingGroup"
    ...
    LoadBalancerNames:
      - !FindInMap [Environments, !Ref EnvironmentValue, ELB]
    MinSize: !FindInMap [Environments, !Ref EnvironmentValue, ASMIN]
    MaxSize: !FindInMap [Environments, !Ref EnvironmentValue, ASMAX]
    VPCZoneIdentifier: !FindInMap [Environments, !Ref EnvironmentValue, Subnets]
```


So that takes care of the different environment values, but what about the special software configuration on the QA instances and the removal of the SSH key on Production? For that we use the conditionals we set earlier:  

___JSON___  
```
"ServerLaunchConfig" : {
  "Type" : "AWS::AutoScaling::LaunchConfiguration",
  "Properties" : {
    ...
    "KeyName" : {
      "Fn::If": [ "ProdNotify",
        {"Ref" : "AWS::NoValue"},
        "app-keypair-name"
    ]},
    "SecurityGroups" : {
      {"Fn::FindInMap" : [
        "Environments",
        { "Ref" : "EnvironmentValue"},
        "SecurityGroup"
        ]
      }},
    ...
    "UserData" : {
      "Fn::Base64" : {
        "Fn::Join" : [
        "\n",
        ["#!/bin/bash -v",
        "command1",
        "command2”,
        { "Fn::If" : [
          "QANotify",
          { "Fn::Join" : [
          "\n",
          ["qacommand1",
          "qacommand2",
          "qacommand3"]
          ]},
          {"Ref" : "AWS::NoValue"}
        ]
      }
```
___YAML___  
```
ServerLaunchConfig:
  Type: AWS::AutoScaling::LaunchConfiguration
  Properties: 
    ...
    KeyName: !If [ProdNotify, !Ref "AWS::NoValue", app-keypair-name]
    SecurityGroups:
      - !FindInMap [Environments, !Ref EnvironmentValue, SecurityGroup]
    ...
    UserData: 
      Fn::Base64: 
        !Join
          - "\n"
          - - "#!/bin/bash -v"
            - "command1"
            - "command2”
            - !If [QANotify, !Join [ "\n", [ "qacommand1", "qacommand2", "qacommand3"]], !Ref "AWS::NoValue"]
```


There are a few different things going on here so let's take them one at a time. The first item of import is the “KeyName” section where we use the “ProdNotify” conditional we set earlier. Remember if the stack creator picks “Prod” from the drop down box then this item is set to true. We use a special CloudFormation function (“Fn::If”) to see if this value is true. If it is NOT, then we use the data provided (“app-keypair-name”). If it is, then we use another CloudFormation element (“Ref” : “AWS::NoValue”) to use no data at all, essentially setting the KeyName value to nothing.  

Secondly, we use the Mappings again to set the “SecurityGroups” value. And finally, we again use the CloudFormation function (“Fn::If”) to see if the stack creator selected QA as the environment. If so, we then add three additional commands to the end of the user data section which are executed after instance creation. If the condition is not true (the stack creator picked a different environment), we use the “Ref” : “AWS::NoValue” element to add nothing.  

I hope this brief overview of CloudFormation Mappings and Conditionals can help you create templates that are cleaner, versatile, and more flexible. By using the power of these two elements, it becomes possible to make your CloudFormation usage more universal, and eliminates the need to have multiple or highly parameterized templates for multiple environment scenarios.  

Learn more about our Cloud and DevOps solutions.
