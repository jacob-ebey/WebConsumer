# WebConsumer
Consuming REST API's has never been easier.

Imagine that we are consuming a service that is located at "http://mytesturl.com/getTestThing" that
returns the following JSON result:
```json
{
    "label": "Test Label",
    "values": [1,2,3,4,5,6,7,8,9]
}
```

If you are starting this project from scratch, you may opt to use BaseResult as your base model class like follows:
```C#
using WebConsumer;

class TestThing : BaseResult
{
    IProperty<string> LabelProperty;
    ICollectionProperty<int> ValuesProperty;

    public TestThing()
    {
        LabelProperty = RegisterProperty("label");
        ValuesProperty = RegisterPropertyCollection("values", s => int.Parse(s));
    }
  
    public string Label { get { return LabelProperty.Value; } }
  
    public IEnumerable<int> Values { get { return ValuesProperty.Value; } }
}
```

If you are wanting to use this package to consume a service but want to use your own object hierarchy you may
accomplish the same result as above with the following:

```C#
public static class ResultFactory
{
    public static BaseResult CreateTestThing()
    {
        var result = ResultBuilder.Create();
        IProperty<string> labelProperty = result.AddProperty("label");
        ICollectionProperty<int> valuesProperty = result.AddPropertyCollection("values", s => int.Parse());
        return result;
    }
}
```

To populate the object with the JSON data just use a JsonRequestConsumer:
```C#
async Task<TestThing> GetTestThingAsync()
{
    var request = new WebRequest<TestThing>(new JsonRequestConsumer(), new Uri("http://mytesturl.com/getTestThing"));
    return await request.GetResultAsync();
}
```
