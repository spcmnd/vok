# Query String Builder

Parses a complex object into URLSearchParams object.

```typescript
export class QueryStringBuilder {
  static buildParametersFromSearch<T>(obj: T): URLSearchParams {
    let params: URLSearchParams = new URLSearchParams();

    if (obj == null) {
      return params;
    }

    QueryStringBuilder.populateSearchParams(params, '', obj);

    return params;
  }

  private static populateArray<T>(
    params: URLSearchParams,
    prefix: string,
    val: Array<T>
  ): void {
    for (let index in val) {
      const key = prefix + '[' + index + ']';
      const value: any = val[index];
      QueryStringBuilder.populateSearchParams(params, key, value);
    }
  }

  private static populateObject<T>(
    params: URLSearchParams,
    prefix: string,
    val: T
  ): void {
    const objectKeys = Object.keys(val) as Array<keyof T>;

    if (prefix) {
      prefix = prefix + '.';
    }

    for (let objKey of objectKeys) {
      const value = val[objKey];
      const key = prefix + (objKey as string);
      QueryStringBuilder.populateSearchParams(params, key, value);
    }
  }

  private static populateSearchParams<T>(
    params: URLSearchParams,
    key: string,
    value: any
  ): void {
    if (value instanceof Array) {
      QueryStringBuilder.populateArray(params, key, value);
    } else if (value instanceof Date) {
      params.set(key, value.toISOString());
    } else if (value instanceof Object) {
      QueryStringBuilder.populateObject(params, key, value);
    } else {
      // Filter unwanted values
      if (value === null || value === undefined || value === '') return;

      params.set(key, value.toString());
    }
  }
}
```
