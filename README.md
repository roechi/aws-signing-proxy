aws-signing-proxy
=================

A transparent proxy to AWS services which uses short-lived credentials retrieved from vault in order to forward requests as signed http request to AWS.

For ready-to-use binaries have a look at releases. Additionally, we provide a docker image which can be used both in a test setup and as a sidecar in kubernetes.

In addition to the proxy you may also use `vault-env-cred-provider` as an [credential provider for AWS tooling](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sourcing-external.html).

# Build & Run

## Local

### Build

#### aws-signing-proxy

1. Change directory to `cmd/aws-signing-proxy`
2. Run `go build`

#### vault-env-cred-provider
1. Change directory to `cmd/vault-env-cred-provider`
2. Run `go build`

### Run

#### aws-signing-proxy

Execute the binary with the required environment variables set:
```
ASP_VAULT_AUTH_TOKEN=someTokenWhichAllowsYouToAccessVault; \
ASP_VAULT_URL=https://vault.url.invalid; \
ASP_TARGET_URL=https://someAWSServiceSupportingSignedHttpRequests; \
ASP_SERVICE=s3; \
AWS_REGION=eu-central-1; \
ASP_VAULT_CREDENTIALS_PATH=/an-aws-engine-in-vault/creds/a-role-defined-aws; \
aws-signing-proxy
```

#### vault-env-cred-provider

This program can be used as a [credential provider for AWS tooling](https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-sourcing-external.html). 
Setting it up is a two-step process:

1. Export the required env variables:
```
export ASP_VAULT_AUTH_TOKEN=someTokenWhichAllowsYouToAccessVault
export ASP_VAULT_URL=https://vault.url.invalid
export ASP_VAULT_CREDENTIALS_PATH=/an-aws-engine-in-vault/creds/a-role-defined-aws
```
2. Create an aws config file with the following contents:
```
[some-aws-profile-name]
credential_process = /path/to/vault-env-cred-provider
```
3. Use AWS cli or sdk using this profile name e.g. some-aws-profile-name. 

Note that:
* You may name the AWS profile `default` so that you don't need to specify which profile to use when using the AWS SDK/CLI.
* There is no need to specify AWS_ACCESS_KEY_ID etc.

### Docker
You can find the built image at: https://hub.docker.com/repository/docker/roechi/aws-signing-proxy
Make sure to provide all required ENV variables (`ASP_VAULT_AUTH_TOKEN`, `ASP_VAULT_URL`, `ASP_TARGET_URL`, `ASP_SERVICE`, `AWS_REGION`, `ASP_VAULT_CREDENTIALS_PATH`).

## License

This project is based on https://github.com/cllunsford/aws-signing-proxy which is licensed as follows:

MIT 2018 (c) Chris Lunsford 

