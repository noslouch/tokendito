## Table of Contents

* [Command line Usage](#command-line-usage)
    * [Default usage](#default-usage)
    * [Multi-tile-Guide](#multi-tile-guide) 
    * [Single-command usage](#single-command-usage)
    * [Additional command line reference](#additional-command-line-reference)
* [Environment variables and user configuration](#environment-variables-and-user-configuration)
    * [Precedence](#precedence)
    * [Environment variables and user configuration table](#environment-variables-and-user-configuration-table)
* [AWS Roles Discovery](#aws-roles-discovery)
* [Supported MFAs](#supported-mfas)
* [To upgrade](#to-upgrade)
* [Installing from github](#installing-from-github) 
* [Troubleshooting](#troubleshooting)
* [Design and Limitations](#design-and-limitations)

# Command line Usage  

## Default usage

Configure your profile by running tokendito with the `--configure` flag, or by populating your `tokendito.ini` file as [here](tokendito.ini.md).
Using --configure will only set the okta_username, okta_todo

Then execute: `tokendito` in your command line.

## Multi-tile Guide

If you have multiple AWS-type Okta tiles assigned to you, please update
your local [tokendito.ini](tokendito.ini.md) file with the links to
your AWS tiles in Okta. You can get the link to your tile by right
clicking on the tile in Okta and selecting \"Copy Link URL.\" This file
supports multiple profiles, in case there is a need to connect with
different Okta Orgs and tiles. tokendito can access the profiles by
name, by passing in the `--profile` parameter.

Without specifying a specific profile, tokendito will look for a default
profile within that file.

## Single-command usage

tokendito accepts all of the necessary parameters to be able to generate
your STS tokens with a single command. There are a couple of ways to do
this!

You can just pass in your information at runtime:

``` sh
tokendito --username prod_service_user@company.com \
--role-arn arn:aws:iam::123456789000:role/dowjones-hammer-engineer \
--mfa-method push \
--okta-aws-tile https://acme.oktapreview.com/home/amazon_aws/b07384d113edec49eaa6/123 \
```

Or you can put your parameters into a single [profile](tokendito.ini.md) and reference that profile.

``` txt
[engineer]
okta_aws_tile = https://acme.oktapreview.com/home/amazon_aws/b07384d113edec49eaa6/123
okta_username = jane.doe@acme.com
mfa_method = push
role_arn = arn:aws:iam::123456789000:role/engineer
```

And execute:

``` sh
tokendito --profile engineer
```

## Additional command line reference 

``` txt
usage: tokendito [-h] [--version] [--configure] [--username OKTA_USERNAME] [--password OKTA_PASSWORD] [--profile USER_CONFIG_PROFILE] [--config-file USER_CONFIG_FILE]
                 [--loglevel {DEBUG,INFO,WARN,ERROR}] [--log-output-file USER_LOG_OUTPUT_FILE] [--aws-config-file AWS_CONFIG_FILE] [--aws-output AWS_OUTPUT]
                 [--aws-profile AWS_PROFILE] [--aws-region AWS_REGION] [--aws-role-arn AWS_ROLE_ARN] [--aws-shared-credentials-file AWS_SHARED_CREDENTIALS_FILE]
                 [--okta-org OKTA_ORG | --okta-tile OKTA_TILE] [--mfa-method MFA_METHOD] [--okta-mfa-response OKTA_MFA_RESPONSE] [--quiet]

Gets a STS token to use with the AWS CLI and SDK.

options:
  -h, --help            show this help message and exit
  --version             Displays version and exit
  --configure           Prompt user for configuration parameters
  --username OKTA_USERNAME
                        username to login to Okta. You can also use the OKTA_USERNAME environment variable.
  --password OKTA_PASSWORD
                        password to login to Okta. You can also user the OKTA_PASSWORD environment variable.
  --profile USER_CONFIG_PROFILE
                        Tokendito configuration profile to use.
  --config-file USER_CONFIG_FILE
                        Use an alternative configuration file. Defaults to tokendito.ini with location depending on the OS.
  --loglevel {DEBUG,INFO,WARN,ERROR}, -l {DEBUG,INFO,WARN,ERROR}
                        [DEBUG|INFO|WARN|ERROR], default loglevel is WARNING.
  --log-output-file USER_LOG_OUTPUT_FILE
                        Optional file to log output to.
  --aws-config-file AWS_CONFIG_FILE
                        AWS Configuration file to write to.
  --aws-output AWS_OUTPUT
                        Sets the output type for the AWS profile.
  --aws-profile AWS_PROFILE
                        AWS profile to save as in the credentials file.
  --aws-region AWS_REGION
                        Sets the region for the AWS profile.
  --aws-role-arn AWS_ROLE_ARN
                        Sets the IAM role.
  --aws-shared-credentials-file AWS_SHARED_CREDENTIALS_FILE
                        AWS credentials file to write to.
  --okta-org OKTA_ORG   Set the Okta Org base URL. This enables role auto-discovery
  --okta-tile OKTA_TILE
                        Okta tile URL to use.
  --mfa-method MFA_METHOD
                        Sets the MFA method
  --okta-mfa-response OKTA_MFA_RESPONSE
                        Sets the MFA response to a challenge
  --quiet               Suppress output```
```
Regarding the Okta password, we are fans of automation but do not
recommend passing in the password to tokendito via plaintext or storing
it in your environment locally.


# Environment variables and user configuration
tokendito supports the use of environment variables and user configuration equivalent to specify the default values for most options.

## Precedence
Credentials and configuration settings take precedence in the following order:  
1) Command line options -- Overrides settings in any other location. You can specify \--username, \--role-arn, \--okta-aws-tile, and \--mfa-method as parameters on the command line.  
2) Environment variables -- You can store values in your system\'s environment variables. It overrides the configuration file.  
3) User configuration file -- The user configuration file is updated when you run the command tokendito \--configure. tokendito uses [platformdirs](https://github.com/platformdirs/platformdirs) to store user configuration in the [tokendito.ini](tokendito.ini.md) file. This file can contain the credential details for the default profile and any named profiles.   

## Environment variables and user configuration table

The following table lists the environment variable and user configuration entry equivalent for the given command line option.
| Command line option | Environment variable | User configuration |
| ------------------- | -------------------- | ------------------ |
| --username | TOKENDITO_OKTA_USERNAME        | okta_username | 
| --password | TOKENDITO_OKTA_PASSWORD |   | 
| --profile  | TOKENDITO_USER_CONFIG_PROFILE | profile |
| --config-file | TOKENDITO_USER_CONFIG_FILE | | 
| --loglevel | TOKENDITO_USER_LOGLEVEL | loglevel | 
| --log-output-file | TOKENDITO_USER_LOG_OUTPUT_FILE        | log_output_file | 
| --aws-config-file | TOKENDITO_AWS_CONFIG_FILE        | aws_config_file | 
| --aws-output | TOKENDITO_AWS_OUTPUT        | aws_output | 
| --aws-profile | TOKENDITO_AWS_PROFILE        | aws_profile | 
| --aws-region | TOKENDITO_AWS_REGION       | aws_region | 
| --aws-role-arn | TOKENDITO_AWS_ROLE_ARN       | aws_role_arn | 
| --aws-shared-credentials-file | TOKENDITO_AWS_SHARED_CREDENTIALS_FILE        | aws_shared_credentials_file | 
| --okta-org | TOKENDITO_OKTA_ORG        | okta_org | 
| --okta-tile | TOKENDITO_OKTA_TILE        | okta_tile | 
| --mfa-method | TOKENDITO_MFA_METHOD        | mfa_method | 
| --okta-mfa-response | TOKENDITO_OKTA_MFA_RESPONSE        | okta_mfa_response | 
| --quiet | TOKENDITO_USER_QUIET        | quiet | 

# AWS Roles Discovery
tokendito will discover all your available AWS Roles configured in Okta, returning a list for you to select from, simply by calling:
```tokendito --okta-org ${YOUR ORG OKTA URL}```

# Supported MFAs

-   Native Okta factors (push, call, sms, TOTP) except Biometrics (FIDO webauthn)
-   Google Authenticator TOTP
-   Duo (push, call, sms, TOTP)

# To upgrade

`pip install --upgrade tokendito`

# Installing from github

`pip install git+ssh://git@github.com/dowjones/tokendito.git@<version>`

For instance,
`pip install git+ssh://git@github.com/dowjones/tokendito.git@2.0.0`

# Troubleshooting

Configuration issues with tokendito can usually be addressed by
validating your environment\'s AWS configuration profile(s) located at:

[\$HOME/.aws/config](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

[\$HOME/.aws/credentials](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-files.html)

[tokendito.ini](tokendito.ini.md)

# Design and Limitations

-   This tool does not cache and reuse Okta session IDs

[Pull requests welcome](CONTRIBUTING.md)!
