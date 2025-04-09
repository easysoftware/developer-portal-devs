# GraphQL API Services & Definitions

This document provides guidance on creating GraphQL API services and defining operations in our application. It outlines how to implement a dedicated API service, structure our GraphQL queries, and define reusable fragments.

---

## GraphQL API Services

GraphQL API services abstract the complexity of communicating with external endpoints. They encapsulate client configuration, error handling, and response parsing so that UI components can focus purely on application logic.

### When to Use an API Service
- **Encapsulation:** Centralize endpoint URLs, authentication, and error logging.
- **Reusability:** Share common query/mutation logic across multiple features.
- **Separation:** Keep HTTP and GraphQL client details isolated from component code.

### Example: DataSourceConfluenceApi

The **dataSourceConfluenceApi.ts** file implements a GraphQL service to interact with the Confluence API. For example:

```ts
import apolloClient from "../../../../shared/plugins/apolloClient";
import { ApolloClient } from "@apollo/client/core";
import { WebDataSourceFragment } from "@/src/easy-ai/dataSourceWeb/shared/graphql/fragments/webDataSource.generated";
import { notifyOnGraphqlValidationErrors } from "@/src/shared/api/utils";
import { addConfluenceDataSource } from "@/src/easy-ai/dataSourceWeb/confluence/graphql/mutations/addConfluenceDataSource";
import {
  AddConfluenceDataSourceMutation,
  AddConfluenceDataSourceMutationVariables,
} from "@/src/easy-ai/dataSourceWeb/confluence/graphql/mutations/addConfluenceDataSource.generated";
import { CollectionConfluenceDataSourcesQuery } from "@/src/easy-ai/dataSourceWeb/confluence/graphql/queries/collectionConfluenceDataSources.generated";
import { collectionConfluenceDataSourcesQuery } from "@/src/easy-ai/dataSourceWeb/confluence/graphql/queries/collectionConfluenceDataSources";

export class DataSourceConfluenceApi {
  constructor(private readonly apolloClient: ApolloClient<object>) {}

  public async addConfluenceDataSource(
    variables: AddConfluenceDataSourceMutationVariables
  ): Promise<WebDataSourceFragment[]> {
    const { data } = await this.apolloClient.mutate<AddConfluenceDataSourceMutation>({
      mutation: addConfluenceDataSource,
      variables,
    });

    notifyOnGraphqlValidationErrors(data?.addConfluenceDataSource);

    return data?.addConfluenceDataSource?.collection?.dataSourceConfluences || [];
  }

  public async getConfluenceDataSources(collectionId: string): Promise<WebDataSourceFragment[]> {
    const response = await this.apolloClient.query<CollectionConfluenceDataSourcesQuery>({
      query: collectionConfluenceDataSourcesQuery,
      variables: {
        collectionId,
      },
    });

    return response.data.easyAiVectorDbCollection?.dataSourceConfluences || [];
  }
}

const dataSourceConfluenceApi = new DataSourceConfluenceApi(apolloClient);

export default dataSourceConfluenceApi;
```

This service hides the details of setting up the GraphQL client and managing errors from the rest of the application.

---

## GraphQL Definitions

GraphQL definitions—queries, mutations, and fragments—should be organized in dedicated files. This promotes reusability and minimizes duplication.

### Best Practices

- **Separation of Concerns:**  
  Keep your API service (responsible for request handling) separate from your GraphQL definitions.

- **File Structure:**
  Keep your GraphQL definitions organized in a dedicated [folder structure](https://easysoftware.stoplight.io/docs/developer-portal-devs/cfd453fb9e5be-frontend-project-structure-and-architecture#32-module-structure) and one graphql definition per file.
  
- **Use of Fragments:**  
  Define fragments for recurring field sets. This avoids redundancy and simplifies future updates. Is possible to use fragments in fragments if it's suitable.

- **Descriptive Naming:**  
  Use clear names for queries, mutations, and fragments. For instance, our query in **collectionConfluenceDataSources.ts** is named `collectionConfluenceDataSourcesQuery` and our reusable fragment defined in **webDataSource.ts** is called `webDataSourceFragment`.
  Name in the graphql definition is the same as const, only without `Fragment`, `Query`, `Mutation` or `Subscription` suffix.

- **Generate Types:**  
  Use code generation tools to create TypeScript types from your GraphQL schema. This ensures type safety and reduces runtime errors. [link](https://easysoftware.stoplight.io/docs/developer-portal-devs/a32e74fbf89d3-frontend-code-generator)

---

### Example: Query and Fragment Definitions

#### GraphQL Fragment: webDataSourceFragment

The **webDataSource.ts** file includes the fragment that defines the common fields for a web data source:

```ts
import { gql } from "@apollo/client";

export const webDataSourceFragment = gql`
  fragment WebDataSource on DataSource {
    id
    status
    author {
      id
      name
    }
    type
    url
    attachment {
      contentUrl
    }
    typeProperties
  }
`;
```

#### GraphQL Query: collectionConfluenceDataSourcesQuery

The **collectionConfluenceDataSources.ts** file contains a query that retrieves a collection of Confluence data sources while reusing the `webDataSourceFragment`:

```ts
import { gql } from "@apollo/client";
import { webDataSourceFragment } from "@/src/easy-ai/dataSourceWeb/shared/graphql/fragments/webDataSource";

export const collectionConfluenceDataSourcesQuery = gql`
  query collectionConfluenceDataSources($collectionId: ID!) {
    easyAiVectorDbCollection(id: $collectionId) {
      dataSourceConfluences {
        ...WebDataSource
      }
    }
  }

  ${webDataSourceFragment}
`;
```

<!-- theme: danger -->
> Note: After each update of some graphql definition is needed to run generation of graphql types. [link](https://easysoftware.stoplight.io/docs/developer-portal-devs/a32e74fbf89d3-frontend-code-generator)

<!-- theme: danger -->
> Note: When defining GraphQL queries with the gql tag, use the tagged template literal syntax without enclosing it in parentheses <code>gql\`...\`;</code>
. Do not write <code>gql(\`...\`);</code> instead, use it directly followed by a template literal as shown in the examples below. Using parentheses will cause a parsing error—such as Uncaught (in promise) GraphQLError: Syntax Error: Unexpected "["—because it interferes with the proper tag processing of the template literal. For further details on tagged template syntax, please see the MDN documentation on tagged templates.

### GraphQL Definitions with fields from plugins

Plugins in our system might be inactive at times. Therefore, before adding their fields to a GraphQL query, we must first check if the plugin is active. For example, the getAssignCoworkersQuery function checks whether the isR4aActive variable is true. If it is, the query includes fields related to R4A; otherwise, those fields are omitted because they are not part of the GraphQL schema. As a result, we cannot use [GraphQL directives](https://graphql.org/learn/queries/#directives) to conditionally include these fields.

```ts
import { gql } from "@apollo/client";

export const getAssignCoworkersQuery = (isR4aActive: boolean) => {
  return gql`query assignCoworker($id: ID!) {
    easySprintBoard(id: $id) {
      id
      users {
        someBaseUserFields
        ${
          isR4aActive
            ? `
            someR4aSpecificUserFields
            `
            : ""
        }
      }
    }
  }`;
};
```
