# Demonstrate persistent tasks dependencies

This repo was generated with `npx create-turbo` and one change was made to `turbo.json` (see commit history).

Run `turbo run lint` to see that lint tasks from all workspaces successfully execute.

This works because this config:

```json
{
    "lint": {
        "dependsOn": ["^dev"]
    }
}
```

means that the `lint` tasks of all workspaces should "depend on" (i.e. execute _after_) the `dev`
tasks of their package dependencies. In many more words:

- `docs` depends on `ui`, `eslint-config-custom`, `tsconfig` packages, so `docs#lint` should execute after `ui#dev`, `eslint-config-custom#dev`, and `tsconfig#dev`.
- `web` depends on `ui`, `eslint-config-custom`, `tsconfig` packages, so `web#lint` should execute after `ui#dev`, `eslint-config-custom#dev`, and `tsconfig#dev`.
- `eslint-config-custom` does not depend on any other packages, so `eslint-config-custom#lint` can execute whenever.
- `tsconfig` does not depend on any other packages, so `tsconfig#lint` can execute whenever.
- `ui` depends on `eslint-config-custom`, `tsconfig` packages, so `ui#lint` should execute after `eslint-config-custom#dev`, and `tsconfig#dev`.

`ui#dev`, `eslint-config-custom#dev`, and `tsconfig#dev` do not exist, so none of the lint tasks
are actually blocked on a persistent task.

So this config can exeucte without any problems!

## How to create the validation error

- You could change to this config:

    ```json
    {
        "lint": {
            "dependsOn": ["dev"]
        }
    }
    ```

    This would indicate that `docs#lint` should depend on `docs#dev`, (which _does_ exist),
    and similarly `web#lint`, should depend on `web#dev`.

- You could also implement a `dev` script in, say `ui`, and it would also cause turbo to throw the validation error.
