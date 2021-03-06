# cfstacks
---
AWSTemplateFormatVersion: "2010-09-09"
Description: "This is cloudformation example1"
Mappings:
  SubnetConfig:
    VPC:
      CIDR: "10.50.0.0/16"
    Subnet1:
      CIDR: "10.50.1.0/24"
    Subnet2:
      CIDR: "10.50.2.0/24"
    Subnet3:
      CIDR: "10.50.3.0/24"
Resources: 
  CFVPC:
    Type: "AWS::EC2::VPC"
    Properties:
      EnableDnsSupport: "true"
      EnableDnsHostnames: "true"
      CidrBlock: !FindInMap [SubnetConfig, VPC, CIDR]
      Tags:  
        - Key: Name
          Value: CFMyVPC
  CFSubnetA:
    Type: "AWS::EC2::Subnet"
    Properties:
      AvailabilityZone: !Select [0, !GetAZs '' ]
      CidrBlock: !FindInMap [SubnetConfig, Subnet1, CIDR]
      VpcId: !Ref CFVPC
      Tags:
        - Key: Name
          Value: my-pub-subnet1
  CFSubnetB:
    Type: "AWS::EC2::Subnet"
    Properties:
      AvailabilityZone: !Select [0, !GetAZs '' ]
      CidrBlock: !FindInMap [SubnetConfig, Subnet2, CIDR]
      VpcId: !Ref CFVPC
      Tags:
        - Key: Name
          Value: my-pri-subnet1
  myRouteTable:
    Type: "AWS::EC2::RouteTable"
    Properties: 
      VpcId: !Ref CFVPC
      Tags:
       - Key: Name
       Value: Web
  myRoute:
    Type: "AWS::EC2::SubnetRouteTableAssociation"
    Properties:
      RouteTableId:

  myCarrierRoute:
    Type: AWS::EC2::Route
    DependsOn: GatewayToInternetAndCarrierNetwork
    Properties:
       RouteTableId:
         Ref: myRouteTable
       DestinationCidrBlock: 0.0.0.0/0
       GatewayId:
         Ref: myCarrierGateway 
Outputs:
  CFVPCID:
    Description: "The VPC ID of CFVPC"
    Value: !Ref CFVPC
    Export:
      Name: !Sub "${AWS::StackName}-CFVPC-ID"
  SubnetAID:
    Description: "The subnet ID of SubnetA"
    Value: !Ref CFSubnetA
    Export:
      Name: !Sub "${AWS::StackName}-my-pub-subnet-ID"
  SubnetBID:
    Description: "The subnet ID of SubnetB"
    Value: !Ref CFSubnetB
    Export:
      Name: !Sub "${AWS::StackName}-my-pri-subnet-ID"
