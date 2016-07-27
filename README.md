---
title: Monitoring Instance Usage
owner: Apps Manager
---

<strong><%= modified_date %></strong>

This topic describes how to retrieve app and service instance usage information. You can access the data using Apps Manager or by calling the Apps Man API from the cf CLI.

## <a id='apps-man'></a>Monitoring Instance Usage from Apps Manager ##

There are two ways to monitor app and service instance usage from Apps Manager. The Accounting Report provides a summarized report, and the Usage Report provides a more detailed view of the data.

To access these reports you must be [logged into the Apps Manager](../customizing/console-login.html) as an admin.

### <a id="accounting-report"></a>View the Accounting Report

The Accounting Report displays instance usage information for all orgs in your [Pivotal Cloud Foundry](https://network.pivotal.io/products/pivotal-cf) (PCF) deployment except the <strong>system</strong> org. The Accounting Report provides a high-level overview of your usage by displaying your monthly count of app instances.

1. Select **Accounting Report** from the left navigation of Apps Manager.

2. View the average and maximum app instances in use per month under **App Instance Statistics**.

  The maximum is the largest number of app instances in use at any one time. The Accounting Report calculates these values from the `start`, `stop`, and `scale` app usage events in Cloud Controller.

<%= image_tag("accounting-report.png") %>

### <a id="usage-report"></a>View the Usage Report

The Usage Report provides a more granular view of your usage by detailing app memory usage and service instance usage information for all spaces in a particular org, including the **system** org.

1. From the **system** org, click **Usage Report**.

    <%= image_tag("usage-report-link.png") %>

    The top of the Usage Report displays total app memory usage and service instance usage by all spaces in your org.

    <%= image_tag("usage-report.png") %>

    **Spaces Details** breaks down your app memory usage and service instance usage by spaces.

    <%= image_tag("service-details.png") %>

2. Click the name of an app to view its **Configuration History**.

    <%= image_tag("app-usage-service.png") %>

## <a id='cf-cli'></a>Monitoring App and Service Usages from the cf CLI ##

This section describes how to use the cf CLI to retrieve information about your app and service instances via the Cloud Controller and Usage service APIs.

### <a id="targetcc"></a>Obtain System Usage Information

Before you can retrieve any app or service information, you must target the Cloud Controller and log in as admin, as follows:

1. Target the endpoint of your Cloud Controller.
<pre class="terminal">$ cf api api.YOUR-DOMAIN</pre>

2. Log in with your credentials.
<pre class="terminal">
$ cf login -u admin
API endpoint:  api.YOUR-DOMAIN
Email: user<span>@</span>domain.com
Password:
Authenticating...
OK
...
Targeted org  YOUR-ORG
Targeted space  development
 API endpoint:    https<span>://ap</span>i.YOUR-DOMAIN (API version:  2.52.0)
 User:            us<span>er@do</span>main.com
 Org:             YOUR-ORG
 Space:           development
</pre>

3. Run `curl` for the `/system_usage_report` on the Usage service.

<pre class="terminal">
curl "https<span>:/</span>/app-usage.YOUR-DOMAIN/system\_usage\_report" -k -v -H "authorization:<span>`cf oauth-token`"</span>"
</pre>

### <a id="individual_org"></a> Obtain Usage Information about an Org ###

To obtain individual org usage information, use the following procedure. You must log in as an admin or as an Org Manager or Org Auditor for the org you want to view.

1. Run `cf login -u USERNAME`.

2. Run `curl` for the `/app_usages` and/or `/service_usages` endpoints on the Usage service.

<pre class="terminal">
$ curl "https<span>:</span>//app-usage.YOUR-DOMAIN/organizations/`cf org YOUR-ORG --guid`/app_usages?start=YYYY-MM-DD&end=YYYY-MM-DD" -k -v -H "authorization: `cf oauth-token`"
{
   "organization_guid": "8d83362f-587a-4148-806b-4407428887b5",
   "period_start": "2016-06-01T00:00:00Z",
   "period_end": "2016-06-13T23:59:59Z",
   "app_usages": [
      {
        app_guid: "00ecd7ce-1dd0-4b3f-63b9-744c9de42afc"
        app_name: "your-app"
        duration_in_seconds: 76730
        instance_count: 6
        memory_in_mb_per_instance: 64
        space_guid: "44435fd6-fbac-5049-bbfc-92d1603a5e98"
        space_name: "outer-space"
      }
   ]
}
</pre>
    and/or
<pre class="terminal">
$ curl "<span>https:</span>//app-usage.YOUR-DOMAIN/organizations/\`cf org YOUR-ORG --guid\`/service\_usages?start=YYYY-MM-DD&end=YYYY-MM-DD" -k -v -H "authorization: \`cf oauth-token`"
{
   "organization\_guid": "8d83362f-587a-4148-806b-4407428887b5",
   "period\_start": "2016-06-01T00:00:00Z",
   "period\_end": "2016-06-13T23:59:59Z",
   "service\_usages": [
      {
        deleted: false
        duration\_in\_seconds: 1377982.52
        service\_guid: "02802293-b769-44cc-807f-eee331ba9b2b"
        service\_instance\_creation: "2016-01-20T18:48:16.000Z"
        service\_instance\_deletion: null
        service\_instance\_guid: "b25b4481-19aa-4504-82c9-f303e7e9ed6e"
        service\_instance\_name: "something-usage-db"
        service\_instance\_type: "managed_service_instance"
        service\_name: "my-mysql-service"
        service\_plan\_guid: "70915a70-7311-4f3e-ab0d-4a7dfd3ef2d9"
        service\_plan\_name: "20gb"
        space\_guid: "e6445eb3-fdac-4049-bafc-94d1703d5e78"
        space\_name: "outer-space"
      }
   ]
}

</pre>

### <a id="retrieve_apps"></a> Retrieve Apps Information ###

1. The `apps` endpoint retrieves information about all of your apps.
<pre class="terminal">$ cf curl /v2/apps
{
   "total\_results": 2,
   "total\_pages": 1,
   "prev\_url": null,
   "next\_url": null,
   "resources": [
      {
         "metadata": {
            "guid": "acf2ce33-ee92-54TY-9adb-55a596a8dcba",
            "url": "/v2/apps/acf2ce33-ee92-54TY-9adb-55a596a8dcba",
            "created\_at": "2016-02-06T17:40:31Z",
            "updated\_at": "2016-02-06T18:09:17Z"
         },
         "entity": {
            "name": "YOUR-APP",
[...]
            "space\_url": "/v2/spaces/a0205ae0-a691-4667-92bc-0d0dd712b6d3",
            "stack\_url": "/v2/stacks/86205f38-84fc-4bc2-b2b8-af7f55669f04",
            "routes\_url": "/v2/apps/acf2ce33-ee92-54TY-9adb-55a596a8dcba/routes",
            "events\_url": "/v2/apps/acf2ce33-ee92-54TY-9adb-55a596a8dcba/events",
            "service\_bindings\_url": "/v2/apps/acf2ce33-ee92-54TY-9adb-55a596a8dcba/service\_bindings",
	            "route\_mappings\_url": "/v2/apps/acf2ce33-ee92-54TY-9adb-55a596a8dcba/route\_mappings"
         }
      },
      {
         "metadata": {
            "guid": "79bb58cc-3737-43be-ac70-39a2843b5178",
            "url": "/v2/apps/79bb58cc-3737-4540-ac70-39a2843b5178",
            "created\_at": "2016-02-15T23:25:47Z",
            "updated\_at": "2016-03-12T21:54:59Z"
         },
         "entity": {
            "name": "ANOTHER-APP",
[...]
            "space\_url": "/v2/spaces/a0205ae0-a691-4667-92bc-0d0dd712b6d3",
            "stack\_url": "/v2/stacks/86205f38-84fc-4bc2-b2b8-af7f55669f04",
            "routes\_url": "/v2/apps/79bb58cc-3737-4540-ac70-39a2843b5178/routes",
            "events\_url": "/v2/apps/79bb58cc-3737-4540-ac70-39a2843b5178/events",
            "service\_bindings\_url": "/v2/apps/79bb58cc-3737-4540-ac70-39a2843b5178/service\_bindings",
            "route\_mappings\_url": "/v2/apps/79bb58cc-3737-4540-ac70-39a2843b5178/route\_mappings"
         }
      }
   ]
}
</pre>
The output of this command provides the URL endpoints for each app, under `metadata: url`. You can use these app-specific endpoints to retrieve more information about that app. In the example above, the endpoints for the two apps are `/v2/apps/acf2ce33-ee92-54TY-9adb-55a596a8dcba` and `/v2/apps/79bb58cc-3737-4540-ac70-39a2843b5178`.

1. The `summary` endpoint under each app-specific URL retrieves the instances and any bound services for that app.
<pre class="terminal">$ cf curl /v2/apps/acf2ce75-ee92-4bb6-9adb-55a596a8dcba/summary
{
   "guid": "acf2ce75-ee92-4bb6-9adb-55a596a8dcba",
   "name": "YOUR-APP",
   "routes": [
      {
         "guid": "7421b6af-75cb-4334-a862-bc5e1ababfe6",
         "host": "YOUR-APP",
         "path": "",
         "domain": {
            "guid": "fb6bd89f-2ed9-49d4-9ad1-97951a573135",
            "name": "YOUR-DOMAIN.io"
         }
      }
   ],
   "running\_instances": 5,
   "services": [
      {
         "guid": "b9cdr456-3c61-4f8a-a307-9b4ty836fb2e",
         "name": "YOUR-APP-db",
         "bound\_app\_count": 1,
         "last\_operation": {
            "type": "create",
            "state": "succeeded",
            "description": "",
            "updated\_at": null,
            "created\_at": "2016-02-05T04:58:46Z"
         },
         "dashboard\_url": "htt<span>ps://</span>cloudfoundry.appdirect.com/api/custom/cloudfoundry/v2/sso/start?serviceUuid=b5cASDF-3c61-4f8a-a307-9bf85j45fb2e",
         "service\_plan": {
            "guid": "fbcec3af-3e8d-4ee7-adfe-3f12a137ed66",
            "name": "turtle",
            "service": {
               "guid": "34dbc753-34ed-4cf1-9a87-a255dfca5339b",
               "label": "elephantsql",
               "provider": null,
               "version": null
[...]
</pre>

1. Use the `app_usages` endpoint to return a report covering app usage within an org or space during a period of time. Form the query as follows:
   * The Cloud Controller API endpoint
   * The string `/2/accounting_report/organizations`
   * Your organization GUID. This is the identifier string that follows "organizations" in the URL to which your Apps Manager redirects.
   * The string `app_usages`.
   * `start=` followed by your start date in YYYY-MM-DD format.
   * `end=` followed by your end date in YYYY-MM-DD format.

      <pre class="terminal">$ cf curl /v2/apps/accounting\_report/organizations/8f88362f-547c-4158-808b-4605468387d5/app\_usages?start=2016-06-01&end=2016-06-13
{
   "organization\_guid": "<SOME\_ORGANIZATION\_GUID>",
   "period\_start": "2016-06-01T00:00:00Z",
   "period\_end": "2016-06-13T23:59:59Z",
   "app\_usages": [
      {
         "space\_guid": "<SOME_SPACE_GUID>,
         "space\_name": "<SOME_SPACE_NAME>",
         "app\_name": "<SOME\_APP\_NAME>",
         "app\_guid": "<SOME\_APP\_GUID>",
         "instance\_count": 6,
         "memory\_in\_mb\_per\_instance": 64,
         "duration\_in\_seconds": 76730
      }
   ]
}
</pre>

### <a id="retrieve_services"></a> Retrieve Services Information ###

Use `cf curl` to rerieve service instance information. The `service_instances?` endpoint retrieves details about both bound and unbound service instances:
<pre class="terminal">
$ cf curl /v2/service\_instances?
{
   "total\_results": 4,
   "total\_pages": 1,
   "prev\_url": null,
   "next\_url": null,
   "resources": [
      {
         "metadata": {
            "guid": "b9cdr456-3c61-4f8a-a307-9b4ty836fb2e",
            "url": "/v2/service\_instances/b9cdr456-3c61-4f8a-a307-9b4ty836fb2e",
            "created\_at": "2016-02-05T04:58:46Z",
            "updated\_at": null
         },
         "entity": {
            "name": "YOUR-BOUND-DB-INSTANCE",
            "credentials": {},
            "service\_plan\_guid": "fbcec3af-3e8d-4ee7-adfe-3f12a137ed66",
            "space\_guid": "a0205ae0-a691-4667-92bc-0d0dd712b6d3",
            "gateway\_data": null,
            "dashboard\_url": "htt<span>ps://cl</span>oudfoundry.appdirect.com/api/custom/cloudfoundry/v2/sso/start?serviceUuid=b9cdr456-3c61-4f8a-a307-9b4ty836fb2e",
            "type": "managed\_service\_instance",
            "last\_operation": {
               "type": "create",
               "state": "succeeded",
               "description": "",
               "updated\_at": null,
               "created\_at": "2016-02-05T04:58:46Z"
            },
            "tags": [],
            "space\_url": "/v2/spaces/a0205ae0-a691-4667-92bc-0d0dd712b6d3",
            "service\_plan\_url": "/v2/service\_plans/fbcec3af-3e8d-4ee7-adfe-3f12a137ed66",
            "service\_bindings\_url": "/v2/service\_instances/b9cdr456-3c61-4f8a-a307-9b4ty836fb2e/service\_bindings",
            "service\_keys\_url": "/v2/service\_instances/b9cdr456-3c61-4f8a-a307-9b4ty836fb2e/service\_keys",
            "routes\_url": "/v2/service\_instances/b9cdr456-3c61-4f8a-a307-9b4ty836fb2e/routes"
         }
      },
      {
         "metadata": {
            "guid": "78be3399-bdc7-4fbf-a1a4-6858a50d0ff3",
            "url": "/v2/service\_instances/78be3399-bdc7-4fbf-a1a4-6858a50d0ff3",
            "created\_at": "2016-02-15T23:45:30Z",
            "updated\_at": null
         },
         "entity": {
            "name": "YOUR-UNBOUND-DB-INSTANCE",
            "credentials": {},
            "service\_plan\_guid": "fbcec3af-3e8d-4ee7-adfe-3f12a137ed66",
            "space\_guid": "a0205ae0-a691-4667-92bc-0d0dd712b6d3",
            "gateway\_data": null,
            "dashboard\_url": "htt<span>ps://clo</span>udfoundry.appdirect.com/api/custom/cloudfoundry/v2/sso/start?serviceUuid=78be3399-bdc7-4fbf-a1a4-6858a50d0ff3",
            "type": "managed\_service\_instance",
            "last\_operation": {
               "type": "create",
               "state": "succeeded",
               "description": "",
               "updated\_at": null,
               "created\_at": "2016-02-15T23:45:30Z"
            },
            "tags": [],
            "space\_url": "/v2/spaces/a0205ae0-a691-4667-92bc-0d0dd712b6d3",
            "service\_plan\_url": "/v2/service\_plans/fbcec3af-3e8d-4ee7-adfe-3f12a137ed66",
            "service\_bindings\_url": "/v2/service\_instances/78be3399-bdc7-4fbf-a1a4-6858a50d0ff3/service\_bindings",
            "service\_keys\_url": "/v2/service\_instances/78be3399-bdc7-4fbf-a1a4-6858a50d0ff3/service\_keys",
            "routes\_url": "/v2/service\_instances/78be3399-bdc7-4fbf-a1a4-6858a50d0ff3/routes"
         }
      },
   ]
}
</pre>
