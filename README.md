# Lateapexearlyspeed.Json.Schema

This is a simple Json schema .Net implementation based on [Json schema](https://json-schema.org/) - draft 2020.12 (latest one by 2023.12)

The json validation functionalities have passed [official json schema test-suite](https://github.com/json-schema-org/JSON-Schema-Test-Suite) for draft 2020.12 (except cases about limitation listed below)

This .Net implementation has good performance compared with existing more popular and excellent .Net implementations in common cases by BenchmarkDotnet result, but please verify in your use case. This implementation will continue to improve it.

In future, this library may be transformed to be open source project.

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

## Output Information

When validation failed, you can check detailed error information by:

- **IsValid**: As summary indicator for passed validation or failed validation.

- **ResultCode**: The specific error type when validation failed.

- **ErrorMessage**: the specific wording for human readable message

- **Keyword**: current keyword when validation failed

- **InstanceLocation**: The location of the JSON value within the instance being validated. The value is a [JSON Pointer](https://datatracker.ietf.org/doc/html/rfc6901).

- **RelativeKeywordLocation**: The relative location of the validating keyword that follows the validation path. The value is a [JSON Pointer](https://datatracker.ietf.org/doc/html/rfc6901), and it includes any by-reference applicators such as "$ref" or "$dynamicRef". Eg:
    ```
    /properties/width/$ref/minimum
    ```

- **SubSchemaRefFullUri**: The absolute, dereferenced location of the validating keyword when validation failed. The value is a full URI using the canonical URI of the relevant schema resource with a [JSON Pointer](https://datatracker.ietf.org/doc/html/rfc6901) fragment, and it doesn't include by-reference applicators such as "$ref" or "$dynamicRef" as non-terminal path components. Eg:

    ```
    https://example.com/schemas/common#/$defs/count/minimum
    ```

- **SchemaResourceBaseUri**: The absolute base URI of referenced json schema resource when validation failed. Eg:
    ```
    https://example.com/schemas/common
    ```

## Performance Tips
Reuse instantiated JsonValidator instances (which basically represent json schema) to validate incoming json instance data if possible in your cases, to gain better performance.

## External json schema document reference support

Besides of internal json schema resource support automatically, implementation supports external schema document reference support by:

```csharp
var jsonValidator = new JsonValidator(jsonSchema);
string externalJsonSchema = File.ReadAllText("schema2.json");
jsonValidator.AddExternalDocument(externalJsonSchema);
ValidationResult validationResult = jsonValidator.Validate(instance);
```

## Custom keyword support

Besides of standard keywords defined in json schema specification, library supports to create custom keyword for additional validation requirement. Eg:

```
{
  "type": "object",
  "properties": {
    "prop1": {
      "customKeyword": "Expected value"
    }
  }
}
```

```csharp
ValidationKeywordRegistry.AddKeyword<CustomKeyword>();
```

```csharp
[Keyword("customKeyword")] // It is your custom keyword name
[JsonConverter(typeof(CustomKeywordJsonConverter))] // Use 'CustomKeywordJsonConverter' to deserialize to 'CustomKeyword' instance out from json schema text
internal class CustomKeyword : KeywordBase
{
    private readonly string _customValue; // Simple example value

    public CustomKeyword(string customValue)
    {
        _customValue = customValue;
    }

    // Do your custom validation work here
    protected override ValidationResult ValidateCore(JsonInstanceElement instance, JsonSchemaOptions options)
    {
        if (instance.ValueKind != JsonValueKind.String)
        {
            return ValidationResult.ValidResult;
        }

        return instance.GetString() == _customValue
            ? ValidationResult.ValidResult
            : ValidationResult.CreateFailedResult(ResultCode.UnexpectedValue, "It is not my expected value.", options.ValidationPathStack, Name, instance.Location);
    }
}
```

```csharp
internal class CustomKeywordJsonConverter : JsonConverter<CustomKeyword>
{
    // Library will input json value of your custom keyword: "customKeyword" to this method.
    public override CustomKeyword? Read(ref Utf8JsonReader reader, Type typeToConvert, JsonSerializerOptions options)
    {
        // Briefly: 
        return new CustomKeyword(reader.GetString()!);
    }

    public override void Write(Utf8JsonWriter writer, CustomKeyword value, JsonSerializerOptions options)
    {
        throw new NotImplementedException();
    }
}
```

### Other extension usage doc is to be continued .

## Limitation

- This implementation focuses on json schema validation, currently not support Annotation
- Due to lack Annotation support, it also not support following keywords: unevaluatedProperties, unevaluatedItems
- Not support format feature and content-encoded string currently

## Issue report

Welcome to raise issue and wishlist, I will try to fix if make sense, thanks !

## More doc is to be written
