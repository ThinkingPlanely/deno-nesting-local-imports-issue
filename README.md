# Recursively Importing Local Packages via "links"

Given the project structure where `app` depends on `@pkg/printer` &
`@pkg/printer` depends on `@pkg/formatter` via the `links` field in `deno.json`:

When running `app` we get the warning:

`"links" field can only be specified in the workspace root deno.json file.`

and error:

`JSR package not found: @pkg/formatter`

When running a test script in `@pkg/printer`, everything works as expected. It's
able to import and use `@pkg/formatter` without any errors.

`app`:

```json
{
    ...
    "imports": {
        "@pkg/printer": "jsr:@pkg/printer@1.0.0"
    },
    "links": [
        "../pkg/printer"
    ]
}
```

`@pkg/printer`:

```json
{
    ...
    "imports": {
        "@pkg/formatter": "jsr:@pkg/formatter@1.0.0"
    },
    "links": [
        "../formatter"
    ]
}
```

Deno needs to recursively read the `deno.json` file for each package in the
`link` property and save the corresponding import maps and `link` properties to
resolve each package's imports. This would resolve the error and the warning
could be removed.
