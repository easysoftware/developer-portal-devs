# Rest API Services

This guide shows how to implement REST API calls using a shared BaseRestApi class.

<!-- theme: danger -->
> Note: Directly using REST in frontend applications is discouraged. For new features, prefer GraphQL endpoints to achieve more efficient, consistent, and flexible data management.

---

## Example: RequiredIssueFieldsApi

```typescript
import { BaseRestApi } from "@/src/shared/api/baseRestApi";
import { RequiredIssueFieldsData } from "@/src/shared/types/requiredIssueFields";

export class RequiredIssueFieldsApi extends BaseRestApi {
  public async saveRequiredIssueFields(issueId: number, data: RequiredIssueFieldsData, urlParams?: string): void {
    const updateUrl = `/issues/${issueId}.json${urlParams ? `?${urlParams}` : ""}`;
    return await this.patch(updateUrl, data);
  }
}

const requiredIssueFieldsApi = new RequiredIssueFieldsApi();
export default requiredIssueFieldsApi;
```
