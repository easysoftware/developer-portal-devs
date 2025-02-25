# Frontend code generator

*Automatic generating typescript types from graphql.*

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
