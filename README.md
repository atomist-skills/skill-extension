## Running a Skill in a Docker Desktop Extension

The following files are needed to turn a Docker Desktop extension into a skill:

-   `datalog` - datalog subscriptions
-   `skill.yaml` - skill metadata
-   `Dockerfile` - copying `skill.yaml` and `datalog` into the file system root
    of the final image; add the `com.docker.skill.api.version="extension/v2"`
    label
-   Add `@atomist/skill@1.0.0-branch-extension.9` to your dependencies

With these changes in place, you can now call `subscribe` in your Extension UI:

```typescript
skill.subscribe(
    {
        namespace: "atomist",
        name: "skill-extension",
        apiKey: "<api key>",
        workspaceId: "<workspace id>",
        debug: true,
    },
    {
        new_issue: async (ctx) => {
            setResponse(
                JSON.stringify(ctx.event.context.subscription.result[0][0])
            );
            return skill.status.completed("Handled new issue event");
        },
    },
    require("pusher-js/with-encryption")
);
```

In order to register the extension as skill, you need to `docker build` and push
the image to your Docker registry and make sure that the image is visible in
your Atomist workspace. When we transact the image, we'll discover the skill
metadata and register the skill.
