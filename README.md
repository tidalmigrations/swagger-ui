# Swagger UI

[![NPM version](https://badge.fury.io/js/swagger-ui.svg)](http://badge.fury.io/js/swagger-ui)

## Deployment for Team Tidal

Current steps to deploy are done manually. Involves, building a docker image, pushing it to ECR and updating (via terraform) the ECS image referenced on the cluster.

### Build docker image

Retrieve the login command to use to authenticate your Docker client to your registry.
Use the AWS CLI:

```
$(aws ecr get-login --no-include-email --region ca-central-1)
```

Note: If you receive an "Unknown options: --no-include-email" error when using the AWS CLI, ensure that you have the latest version installed.

Build your Docker image using the following command:

```
git_sha=$(git rev-parse HEAD)
docker build -t 535558409775.dkr.ecr.ca-central-1.amazonaws.com/swagger:$git_sha
```

### Push image to ECR

Run the following command to push this image to your newly created AWS repository:

```
docker push 535558409775.dkr.ecr.ca-central-1.amazonaws.com/swagger:$git_sha
```

This should result in an image version residing here:
https://ca-central-1.console.aws.amazon.com/ecr/repositories/swagger/?region=ca-central-1

### Deployment

Deployment is done by updating the value for the `version` variable configured via Terraform - https://github.com/tidalmigrations/infrastructure-live/blob/master/main/ca-central-1/production/services/swagger/terraform.tfvars#L46

- Update the value in the `terraform.tfvars` file  to the value of `$git_sha`. ie. the same value that the docker image was tagged with when it was created in the earlier steps.
- Follow the standard terraform deployment pattern and execute:
    - `terragrunt plan` - to confirm the changes being made.
    - `terragrunt apply` - to make the change and update the version
    - commit the version change to git and push to master for the `infrastructure-modules` repository

## New!

**This is the new version of swagger-ui, 3.x. Want to learn more? Check out our [FAQ](http://swagger.io/new-ui-faq/).**

**👉🏼 Want to score an easy open-source contribution?** Check out our [Good first issue](https://github.com/swagger-api/swagger-ui/issues?q=is%3Aissue+is%3Aopen+label%3A%22Good+first+issue%22) label.

As a brand new version, written from the ground up, there are some known issues and unimplemented features. Check out the [Known Issues](#known-issues) section for more details.

This repository publishes to two different NPM modules:

* [swagger-ui](https://www.npmjs.com/package/swagger-ui) is a traditional npm module intended for use in JavaScript web application projects that are capable of resolving dependencies (via Webpack, Browserify, etc).
* [swagger-ui-dist](https://www.npmjs.com/package/swagger-ui-dist) is a dependency-free module that includes everything you need to serve Swagger-UI in a server-side project, or a web project that can't resolve npm module dependencies.

For the older version of swagger-ui, refer to the [*2.x branch*](https://github.com/swagger-api/swagger-ui/tree/2.x).

## Compatibility
The OpenAPI Specification has undergone 5 revisions since initial creation in 2010.  Compatibility between swagger-ui and the OpenAPI Specification is as follows:

Swagger UI Version | Release Date | OpenAPI Spec compatibility | Notes
------------------ | ------------ | -------------------------- | -----
3.10.0 | 2018-02-09 | 2.0, 3.0 | [tag v3.10.0](https://github.com/swagger-api/swagger-ui/tree/v3.10.0)
3.0.21 | 2017-07-26 | 2.0 | [tag v3.0.21](https://github.com/swagger-api/swagger-ui/tree/v3.0.21)
2.2.10 | 2017-01-04 | 1.1, 1.2, 2.0 | [tag v2.2.10](https://github.com/swagger-api/swagger-ui/tree/v2.2.10)
2.1.5 | 2016-07-20 | 1.1, 1.2, 2.0 | [tag v2.1.5](https://github.com/swagger-api/swagger-ui/tree/v2.1.5)
2.0.24 | 2014-09-12 | 1.1, 1.2 | [tag v2.0.24](https://github.com/swagger-api/swagger-ui/tree/v2.0.24)
1.0.13 | 2013-03-08 | 1.1, 1.2 | [tag v1.0.13](https://github.com/swagger-api/swagger-ui/tree/v1.0.13)
1.0.1 | 2011-10-11 | 1.0, 1.1 | [tag v1.0.1](https://github.com/swagger-api/swagger-ui/tree/v1.0.1)

## Documentation

#### Usage
- [Installation](docs/usage/installation.md)
- [Configuration](docs/usage/configuration.md)
- [CORS](docs/usage/cors.md)
- [OAuth2](docs/usage/oauth2.md)
- [Deep Linking](docs/usage/deep-linking.md)
- [Limitations](docs/usage/limitations.md)
- [Version detection](docs/usage/version-detection.md)

#### Customization
- [Overview](docs/customization/overview.md)
- [Plugin API](docs/customization/plugin-api.md)
- [Custom layout](docs/customization/custom-layout.md)

#### Development
- [Setting up](docs/development/setting-up.md)
- [Scripts](docs/development/scripts.md)

##### Integration Tests

You will need JDK of version 7 or higher as instructed here
http://nightwatchjs.org/gettingstarted#selenium-server-setup

Integration tests can be run locally with `npm run e2e` - be sure you aren't running a dev server when testing!

### Browser support
Swagger UI works in the latest versions of Chrome, Safari, Firefox, Edge and IE11.

### Known Issues

To help with the migration, here are the currently known issues with 3.X. This list will update regularly, and will not include features that were not implemented in previous versions.

- Only part of the parameters previously supported are available.
- The JSON Form Editor is not implemented.
- Support for `collectionFormat` is partial.
- l10n (translations) is not implemented.
- Relative path support for external files is not implemented.

## Security contact

Please disclose any security-related issues or vulnerabilities by emailing [security@swagger.io](mailto:security@swagger.io), instead of using the public issue tracker.

## License

Copyright 2017 SmartBear Software

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at [apache.org/licenses/LICENSE-2.0](http://www.apache.org/licenses/LICENSE-2.0)

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
