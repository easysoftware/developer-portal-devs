# GraphQL API Services & Definitions

This document provides guidance on creating GraphQL API services and defining operations in our application. It outlines how to implement a dedicated API service, structure our GraphQL queries, and define reusable fragments.

---

## GraphQL API Services

GraphQL API services abstract the complexity of communicating with external endpoints. They encapsulate client configuration, error handling, and response parsing so that UI components can focus purely on application logic.

### When to Use an API Service
- **Encapsulation:** Centralize endpoint URLs, authentication, and error logging.
- **Reusability:** Share common query/mutation logic across multiple features.
- **Separation:** Keep HTTP and GraphQL client details isolated from component code.

### Example: ProjectStatusReportApi

The **projectStatusReportApi.ts** file implements a GraphQL service to manage project status reports. It uses the Apollo Client to perform queries, mutations, and subscriptions, while also handling errors and notifications.

```ts
import { ApolloClient, FetchResult } from "@apollo/client/core";
import apolloClient from "@/src/shared/plugins/apolloClient";
import { notifyOnGraphqlValidationErrors } from "@/src/shared/api/utils";
import { ProjectStatusReportQuery } from "@/src/easy-ai/projectStatusReport/detail/graphql/queries/projectStatusReport.generated";
import { projectStatusReportQuery } from "@/src/easy-ai/projectStatusReport/detail/graphql/queries/projectStatusReport";
import { FullProjectStatusReportFragment } from "@/src/easy-ai/projectStatusReport/detail/graphql/fragments/fullProjectStatusReport.generated";
import { useSubscription, UseSubscriptionReturn } from "@vue/apollo-composable";
import { projectStatusReportUpdatedSubscription } from "@/src/easy-ai/projectStatusReport/detail/graphql/subscriptions/projectStatusReportUpdated";
import { DeleteProjectStatusReportMutation } from "@/src/easy-ai/projectStatusReport/detail/graphql/mutation/deleteProjectStatusReport.generated";
import { deleteProjectStatusReportMutation } from "@/src/easy-ai/projectStatusReport/detail/graphql/mutation/deleteProjectStatusReport";
import {
  ProjectStatusReportUpdatedSubscription,
  ProjectStatusReportUpdatedSubscriptionVariables,
} from "@/src/easy-ai/projectStatusReport/detail/graphql/subscriptions/projectStatusReportUpdated.generated";

export class ProjectStatusReportApi {
  constructor(private readonly apolloClient: ApolloClient<object>) {}

  getProjectStatusReport = async (projectStatusReportId: string): Promise<FullProjectStatusReportFragment | null> => {
    const { data }: FetchResult<ProjectStatusReportQuery> = await this.apolloClient.query({
      query: projectStatusReportQuery,
      variables: {
        id: projectStatusReportId,
      },
    });

    return data?.easyAiProjectStatusReport || null;
  };

  deleteProjectStatusReport = async (projectStatusReportId: string): Promise<void> => {
    const { data }: FetchResult<DeleteProjectStatusReportMutation> = await this.apolloClient.mutate({
      mutation: deleteProjectStatusReportMutation,
      variables: {
        id: projectStatusReportId,
      },
    });

    notifyOnGraphqlValidationErrors(data?.deleteEasyAiProjectStatusReport);
  };

  useProjectStatusReportSubscription = (
    projectStatusReportId: string
  ): UseSubscriptionReturn<ProjectStatusReportUpdatedSubscription, ProjectStatusReportUpdatedSubscriptionVariables> => {
    return useSubscription(projectStatusReportUpdatedSubscription, { id: projectStatusReportId });
  };
}

export default new ProjectStatusReportApi(apolloClient);
```

This service hides the details of setting up the GraphQL client and managing errors from the rest of the application.

### Example: Using of useProjectStatusReportSubscription method

This example shows how to use the `useProjectStatusReportSubscription` method from the `ProjectStatusReportApi` service to subscribe to updates for a specific project status report. It also demonstrates how to handle the subscription lifecycle, including stopping the previous subscription if it exists.

```ts
public initSubscription = (projectStatusReportId: string) => {
  if (this.stopCurrentSubscription) {
    this.stopCurrentSubscription();
  }

  if (this.shouldSubscribe()) {
    const { onResult, stop } = projectStatusReportApi.useProjectStatusReportSubscription(projectStatusReportId);
    this.stopCurrentSubscription = stop;
    onResult(this.processProjectStatusReportSubscription);
  }
};

private processProjectStatusReportSubscription = (response: FetchResult<ProjectStatusReportUpdatedSubscription>) => {
  if (!response.data?.easyAiProjectStatusReportUpdated) return;
  this.activeProjectStatusReport.value = response.data?.easyAiProjectStatusReportUpdated;
};
```

---

## GraphQL Definitions

GraphQL definitions—queries, mutations, and fragments—should be organized in dedicated files. This promotes reusability and minimizes duplication.

### Best Practices

- **Separation of Concerns:**  
  Keep your API service (responsible for request handling) separate from your GraphQL definitions.

- **File Structure:**
  Keep your GraphQL definitions organized in a dedicated [folder structure](https://easysoftware.stoplight.io/docs/developer-portal-devs/cfd453fb9e5be-frontend-project-structure-and-architecture#32-module-structure) and one graphql definition per file.

- **Use of Fragments:**  
  Define fragments for recurring field sets. This avoids redundancy and simplifies future updates. It is possible to nest fragments if suitable.

- **Descriptive Naming:**  
  Use clear names for queries, mutations, and fragments. For instance, our query in **collectionConfluenceDataSources.ts** is named `collectionConfluenceDataSourcesQuery` and our reusable fragment defined in **webDataSource.ts** is called `webDataSourceFragment`.
  Name in the graphql definition is the same as const, only without `Fragment`, `Query`, `Mutation` or `Subscription` suffix.

- **Generate Types:**  
  Use code generation tools to create TypeScript types from your GraphQL schema. This ensures type safety and reduces runtime errors. [link](https://easysoftware.stoplight.io/docs/developer-portal-devs/a32e74fbf89d3-frontend-code-generator)

---

### Example of definitions

#### GraphQL Fragment: fullProjectStatusReport

The **fullProjectStatusReport.ts** file defines a fragment that includes all necessary fields for a project status report. This fragment can be reused in queries and mutations to ensure consistency and reduce duplication. Inside the fragment we can use other fragments to avoid duplication of code. For example, we can use `projectStatusReportBlockDataFragment` and `repeatingTemplateFragment` to include specific fields related to project status report blocks and repeating templates.

```ts
import { gql } from "@apollo/client";
import { projectStatusReportBlockDataFragment } from "@/src/easy-ai/projectStatusReport/detail/graphql/fragments/projectStatusReportBlockData";
import { repeatingTemplateFragment } from "@/src/easy-ai/projectStatusReport/detail/graphql/fragments/repeatingTemplateFragment";

export const fullProjectStatusReportFragment = gql`
  fragment FullProjectStatusReport on ProjectStatusReport {
    blocksToReport
    createdAt
    blocksData {
      ...ProjectStatusReportBlockData
    }
    id
    interval
    manageable
    name
    repeatingTemplate {
      ...RepeatingTemplate
    }
    project {
      identifier
    }
  }

  ${projectStatusReportBlockDataFragment}
  ${repeatingTemplateFragment}
`;
```

#### GraphQL Query: projectStatusReport

The **projectStatusReport.ts** file defines a query to fetch a project status report, using the `fullProjectStatusReportFragment`:

```ts
import { gql } from "@apollo/client";
import { fullProjectStatusReportFragment } from "@/src/easy-ai/projectStatusReport/detail/graphql/fragments/fullProjectStatusReport";

export const projectStatusReportQuery = gql`
  query projectStatusReport($id: ID!) {
    easyAiProjectStatusReport(id: $id) {
      ...FullProjectStatusReport
    }
  }

  ${fullProjectStatusReportFragment}
`;
```

#### GraphQL Mutation: deleteProjectStatusReport

The **deleteProjectStatusReport.ts** file defines a mutation to delete a project status report, reusing the `easyErrorFragment` for error handling:

```ts
import { gql } from "@apollo/client";
import { easyErrorFragment } from "@/src/shared/graphql/fragments/error";

export const deleteProjectStatusReportMutation = gql`
  mutation deleteProjectStatusReport($id: ID!) {
    deleteEasyAiProjectStatusReport(id: $id) {
      errors {
        ...EasyError
      }
    }
  }

  ${easyErrorFragment}
`;
```

<!-- theme: danger -->
> Note: Each mutation must return an `errors` field that contains the `EasyError` fragment. This is crucial for proper error handling.

#### GraphQL Subscription: projectStatusReportUpdated

The **projectStatusReportUpdated.ts** file defines a subscription to listen for updates to a project status report, using the `fullProjectStatusReportFragment`:

```ts
import { gql } from "@apollo/client";
import { fullProjectStatusReportFragment } from "@/src/easy-ai/projectStatusReport/detail/graphql/fragments/fullProjectStatusReport";

export const projectStatusReportUpdatedSubscription = gql`
  subscription projectStatusReportUpdated($id: ID!) {
    easyAiProjectStatusReportUpdated(id: $id) {
      ...FullProjectStatusReport
    }
  }

  ${fullProjectStatusReportFragment}
`;
```

<!-- theme: danger -->
> Note: After each update of some graphql definition is needed to run generation of graphql types. [link](https://easysoftware.stoplight.io/docs/developer-portal-devs/a32e74fbf89d3-frontend-code-generator)

<!-- theme: danger -->
> Note: When defining GraphQL with the gql tag, use the tagged template literal syntax without enclosing it in parentheses <code>gql\`...\`;</code>. Do not write <code>gql(\`...\`);</code> instead, use it directly followed by a template literal as shown in the examples below. Using parentheses will cause a parsing error—such as Uncaught (in promise) GraphQLError: Syntax Error: Unexpected "["—because it interferes with the proper tag processing of the template literal. For further details on tagged template syntax, please see the MDN documentation on tagged templates.

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
