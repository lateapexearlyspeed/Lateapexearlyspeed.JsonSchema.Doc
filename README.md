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

### Output Information

When validation failed, you can check detailed error information by:

- IsValid: As summary indicator for passed validation or failed validation.

- ResultCode: The specific error type when validation failed.

- ErrorMessage: the specific wording for human readable message

- Keyword: current keyword when validation failed

- InstanceLocation: The location of the JSON value within the instance being validated. The value is a JSON Pointer.

- RelativeKeywordLocation: The relative location of the validating keyword that follows the validation path. The value is a JSON Pointer, and it includes any by-reference applicators such as "$ref" or "$dynamicRef". Eg:
    ```
    /properties/width/$ref/minimum
    ```

- SubSchemaRefFullUri: The absolute, dereferenced location of the validating keyword when validation failed. The value is a full URI using the canonical URI of the relevant schema resource with a JSON Pointer fragment, and it doesn't include by-reference applicators such as "$ref" or "$dynamicRef" as non-terminal path components. Eg:

    ```
    https://example.com/schemas/common#/$defs/count/minimum
    ```

- SchemaResourceBaseUri: The absolute base URI of referenced json schema resource when validation failed. Eg:
    ```
    https://example.com/schemas/common
    ```

### Recommendation
Reuse instantiated JsonValidator instances (which basically represent json schema) to validate incoming json instance data if possible in your cases, to gain better performance.

## External json schema document reference support

Besides of internal json schema resource support automatically, implementation supports external schema document reference support by:

```csharp
var jsonValidator = new JsonValidator(jsonSchema);
string externalJsonSchema = File.ReadAllText("schema2.json");
jsonValidator.AddExternalDocument(externalJsonSchema);
ValidationResult validationResult = jsonValidator.Validate(instance);
```

### Other extension usage doc is to be continued .

## Limitation

- This implementation focuses on json schema validation, currently not support Annotation
- Due to lack Annotation support, it also not support following keywords: unevaluatedProperties, unevaluatedItems
- Not support format feature and content-encoded string currently

## Issue report

Welcome to raise issue and wishlist, I will try to fix if make sense, thanks !

## More doc is to be written
