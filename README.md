# Serverless Aws Auto Resource Names

This is a plugin for the [Serverless Framework](https://serverless.com/).
When "aws" is used as the provider, this plugin will automatically
insert names for the resources, and if enabled exports, for which none are specified.

## Name Generation

Names are generated by prepended the specified prefix and appending the logical
resource name converted to kebab case. If a resource doesnt have a name specified the generated on is inserted,
but will never overwrite a specified one. This also holds for outputs.

### Examples

#### Using the config

````
custom:
    awsAutoResourceNames:
        prefix: ${self:service}_${self:provider.stage}_ # myservice_dev_
        exportPrefix: ${self:service}_${self:provider.stage}_export_ # myservice_dev_export_
        generateExports: true
````
will have
````
MyTestRepo:
    Type: AWS::CodeCommit::Repository
````

go to

````
MyTestRepo:
    Type: AWS::CodeCommit::Repository
    Properties:
        RepositoryName: myservice_dev_my-test-repo
````

and

````
Outputs:
    DebugValue:
        Value: ...
````

go to

````
Outputs:
    DebugValue:
        Value: ...
        Export:
            Name: myservice_dev_export_debug-value
````

### Special Cases

Name generation is explicitly defined on a type to type basis, therefore some types might not have names automatically inserted.
In which case those will be added in future version. And here are some special type behaviour cases.

``AWS::S3::Bucket`` - names will have underscores replaced by dots, since buckets cant handle underscores.

## Configuration

Configuration properties have to be specified under ``custom.awsAutoResourceNames`` like this
````
custom:
    awsAutoResourceNames:
            [config property]: [config property value]
            ...
    ...
````

#### ``prefix: String`` ``Default: none``
The prefix that is prepended before each generated resource name.

#### ``exportPrefix: String?`` ``Default: none``

The prefix used for the generation of output export name generation when specified.

#### ``generateExports: Boolean?`` ``Default: false``

When true, it will generate names for all outputs, which have not specified an export, therefore exporting all of them.