# Frontend code generator

*Automatic generating typescript types from graphql and swagger schema.*

---

## GraphQL Code Generator

Graphql codegenerator is a package, that provide feature of generating typescript files from graphql schema. [doc](https://the-guild.dev/graphql/codegen/docs/getting-started)

### How to generate types

```bash
rake easyproject:graphql_generate
```

With this command, we generate git-ignored files in the `app/frontend/src/shared/graphql_schema` folder. This file contains GraphQL schema from which we generate TypeScript types into `app/frontend/src/shared/types/schemaTypes.generated.ts`. Additionally, for each GraphQL query/mutation definition file, a separate type file is generated. These generated types can be used in the TypeScript code. Each generated file has the .generated.ts suffix. After generating the types, we can commit and push them with Git."

### Using generated types

Example of using `EasyProductBacklogItemMutation` and `EasyProductBacklogItemAttrs` generated types in api service `PbiApi`:

```ts
import { ApolloClient } from "@apollo/client/core";
import apolloClient from "../../../shared/plugins/apolloClient";
import { COLUMN_TO_REFINE } from "../constants";
import { handleErrors } from "../../../shared/api/utils";
import { ApiResult } from "../../../shared/types/graphql";
import { createUpdateProductBacklogItemMutation } from "../graphql/productBacklogItem";

import { EasyProductBacklogItemMutation } from "../graphql/productBacklogItem.generated";
import { EasyProductBacklogItemAttrs } from "../../../shared/types/schemaTypes.generated";

export class PbiApi {
  constructor(private readonly apolloClient: ApolloClient<object>) {}

  public async createPbi(pbi: EasyProductBacklogItemAttrs) {
    const { data, errors } = await this.apolloClient.mutate<EasyProductBacklogItemMutation>({
      mutation: createUpdateProductBacklogItemMutation,
      variables: {
        attributes: {
          status: COLUMN_TO_REFINE.key,
          ...pbi,
        },
      },
    });

    handleErrors(data?.easyProductBacklogItem as ApiResult, errors);
  }
}

const pbiApi = new PbiApi(apolloClient);
export default pbiApi;
```

---

## Openapi Code Generator

Openapi codegenerator is a package, that provide feature of generating typescript types and api services from swagger schema. [doc](https://openapi-generator.tech/docs/generators/typescript-fetch)

### How to generate types

```bash
rake easyproject:openapi_generate
```

With this command, we clean the app/frontend/src/shared/openapi folder and generate a git-ignored file named easy_swagger.yml. This file contains the Swagger schema from which we generate TypeScript types and services into app/frontend/src/shared/openapi/. The last step performed by the rake task is to edit the file app/frontend/src/shared/openapi/runtime.ts to support window.urlPrefix.

### Using generated files

Example of using `IssueApi`

```ts
import { GetIssueFormatEnum, IssueApi, IssueApiResponse } from "@/src/shared/openapi";

const issueId = 1;
const issueApi = new IssueApi();
const { issue }: { issue: IssueApiResponse } = await issueApi.getIssue({
  id: issueId,
  format: GetIssueFormatEnum.Json,
});
```
