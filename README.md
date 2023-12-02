# Lateapexearlyspeed.Json.Schema

This is a simple Json schema .Net implementation based on [Json schema](https://json-schema.org/) - draft 2020.12 (latest one by 2023.12)

The json validation functionalities have passed [official json schema test-suite](https://github.com/json-schema-org/JSON-Schema-Test-Suite) for draft 2020.12 (except cases about limitation listed below)

This .Net implementation has good performance compared with existing more popular and excellent .Net implementations in common cases by BenchmarkDotnet result, but please verify in your use case. This implementation will continue to improve it.

## Basic Usage

```
Install-Package LateApexEarlySpeed.Json.Schema
```

```csharp
string jsonSchema = File.ReadAllText("schema.json");
string instance = File.ReadAllText("instance.json");

var jsonValidator = new JsonValidator(jsonSchema);
ValidationResult validationResult = jsonValidator.Validate(instance);

if (validationResult.IsValid)
{
    Console.WriteLine("good");
}
else
{
    Console.WriteLine($"Failed keyword: {validationResult.Keyword}");
    Console.WriteLine($"ResultCode: {validationResult.ResultCode}");
    Console.WriteLine($"Error message: {validationResult.ErrorMessage}");
    Console.WriteLine($"Failed instance location: {validationResult.InstanceLocation}");
    Console.WriteLine($"Failed relative keyword location: {validationResult.RelativeKeywordLocation}");
    Console.WriteLine($"Failed schema resource base uri: {validationResult.SchemaResourceBaseUri}");
}
```

### Recommendation
Reuse instantiated JsonValidator instances (which basically represent json schema) to validate incoming json instance data if possible in your cases, to gain better performance.

## External json schema document reference support

Besides of internal json schema resource support automatically, implementation supports external schema document reference support by:

```csharp
var jsonValidator = new JsonValidator(jsonSchema);
string externalJsonSchema = File.ReadAllText("schema2.json");
jsonValidator.AddExternalDocument(externalJsonSchema);
```

### Other extension usage doc is to be continued .

## Limitation

- This implementation focuses on json schema validation, currently not support Annotation
- Due to lack Annotation support, it also not support following keywords: unevaluatedProperties, unevaluatedItems
- Not support format feature and content-encoded string currently

## Issue report

Welcome to raise issue and wishlist, I will try to fix if make sense, thanks !

## More doc is to be written
