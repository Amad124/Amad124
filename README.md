{
    "AWSTemplateFormatVersion": "2010-09-09",
    "Description": "Stack to create resources for security teams. Based on Cloudformations minimal permissions for groups. ",
    "Parameters": {
        "Password": {
            "NoEcho": "true",
            "Type": "String",
            "Description": "New account password",
            "Minlength": "1",
            "Maxlength": "41",
            "ConstraintDescription": "the password must be between 1 and 41 characters"
        }
    },
    "Resources": {
        "CFNUser": {
            "Type": "AWS::IAM:User",
            "Properties": {
                "LoginProfile": {
                    "Password": {
                        "Ref": "Password"
                    }
                }
            }
        },
        "CFNUserGroup": {
            "Type": "AWS::IAM::Group"
        },
        "CFNAdminGroup": {
            "Type": "AWS::IAM::Group"
        },
        "Users": {
            "Type": "AWS::IAM::UserToGroupAddition",
            "Properties": {
                "GroupName": {
                    "Ref": "CFNUserGroup"
                },
                "Users": [
                    {
                        "Ref": "CFNUser"
                    }
                ]
            }
        },
        "Admins": {
            "Type": "AWS:IAM:UserToGroupAddition",
            "Properties": {
                "GroupName": {
                    "Ref": "CFNAdminGroup"
                },
                "Users": [
                    {
                        "Ref": "CFNUser"
                    }
                ]
            }
        },
        "CFNUserPolicies": {
            "Type": "AWS::IAM::UserPolicy",
            "Properties": {
                "PolicyName": "CFNUsers",
                "PolicyDocument": {
                    "Statement": [
                        {
                            "Effect": "Allow",
                            "Action": [
                                "cloudformation:Describe*",
                                "cloudformation:List*",
                                "cloudformation:Get*"
                            ],
                            "Resource": "*"
                        }
                    ]
                },
                "Groups": [
                    {
                        "Ref": "CFNUserGroup"
                    }
                ]
            }
        },
        "CFNAdminPolicies": {
            "Type": "AWS::IAM::Policy",
            "Properties": {
                "PolicyName": "CFNAdmins",
                "PolicyDocument": {
                    "Statement": [
                        {
                            "Effect": "Allow",
                            "Action": "cloudformation:*",
                            "Resource": "*"
                        }
                    ]
                },
                "Groups": [
                    {
                        "Ref": "CFNAdminGroup"
                    }
                ]
            },
            "CFNKeys": {
                "Type": "AWS::IAM::Accesskey",
                "Properties": {
                    "UserName": {
                        "Ref": "CFNUser"
                    }
                }
            }
        },
        "Outputs": {
            "AccessKey": {
                "Value": {
                    "Ref": "CFNKeys"
                },
                "Description": "AWSAccessKeyId of new user"
            },
            "SecretKey": {
                "Value": {
                    "Fn:GetAtt": [
                        "CFNKeys",
                        "SecretAccessKey"
                    ]
                },
                "Description": "AWSSecretKey of new user"
            }
        }
    }
}
