{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "webAppNameOne": {
      "type": "string",
      "metadata":{
        "description":"The name of the first web app"
      }
    },
    "AppServicePlanName": {
      "type": "string",
      "metadata":{
        "description": "App Service plan Name"
      }
    }
  },
  "variables":{
    "webAppNameOne":"[toLower(concat(parameters('webAppNameOne'),'-webapp1'))]",
    "webAppPlan":"[concat(parameters('AppServicePlanName'),'-ASP')]",
    "webAppPlanId":"[resourceId('Microsoft.Web/serverfarms',variables('webAppPlan'))]",
    "webAppInsightsOne":"[concat('microsoft.insights/components/',variables('webAppNameOne'))]",
    "errorLinkOne":"[concat('https://',variables('webAppInsightsOne'),'.scm.azurewebsites.net/detectors')]"
  },
  "resources": [
    {
      "apiVersion": "2018-11-01",
      "name": "[variables('webAppNameOne')]",
      "type": "Microsoft.Web/sites",
      "location": "westeurope",
      "tags": {},
      "dependsOn": [
        "[variables('webAppInsightsOne')]",
        "[variables('webAppPlanId')]"
      ],
      "properties": {
        "name": "[variables('webAppNameOne')]",
        "siteConfig": {
          "appSettings": [
            {
              "name": "APPINSIGHTS_INSTRUMENTATIONKEY",
              "value": "[reference(variables('webAppInsightsOne'), '2015-05-01').InstrumentationKey]"
            },
            {
              "name": "ApplicationInsightsAgent_EXTENSION_VERSION",
              "value": "~2"
            },
            {
              "name": "XDT_MicrosoftApplicationInsights_Mode",
              "value": "default"
            },
            {
              "name": "DiagnosticServices_EXTENSION_VERSION",
              "value": "disabled"
            },
            {
              "name": "APPINSIGHTS_PROFILERFEATURE_VERSION",
              "value": "disabled"
            },
            {
              "name": "APPINSIGHTS_SNAPSHOTFEATURE_VERSION",
              "value": "disabled"
            },
            {
              "name": "InstrumentationEngine_EXTENSION_VERSION",
              "value": "disabled"
            },
            {
              "name": "SnapshotDebugger_EXTENSION_VERSION",
              "value": "disabled"
            },
            {
              "name": "XDT_MicrosoftApplicationInsights_BaseExtensions",
              "value": "disabled"
            },
            {
              "name": "ANCM_ADDITIONAL_ERROR_PAGE_LINK",
              "value": "[variables('errorLinkOne')]"
            }
          ],
          "metadata": [
            {
              "name": "CURRENT_STACK",
              "value": "dotnetcore"
            }
          ],
          "alwaysOn": false
        },
        "serverFarmId": "[variables('webAppPlanId')]",
        "clientAffinityEnabled": true
      }
    },
    {
      "apiVersion": "2018-02-01",
      "name": "[variables('webAppPlan')]",
      "type": "Microsoft.Web/serverfarms",
      "location": "westeurope",
      "kind": "",
      "tags": {},
      "dependsOn": [],
      "properties": {
        "name": "[variables('webAppPlan')]",
        "workerSize": "1",
        "workerSizeId": "0",
        "numberOfWorkers": "1",
        "hostingEnvironment": ""
      },
      "sku": {
        "Tier": "Free",
        "Name": "F1"
      }
    },
    {
      "apiVersion": "2015-05-01",
      "name": "[variables('webAppNameOne')]",
      "type": "microsoft.insights/components",
      "kind":"string",
      "location": "westeurope",
      "tags": {},
      "properties": {
        "ApplicationId": "[variables('webAppNameOne')]",
        "Application_Type": "web",
        "Request_Source": "rest"
      }
    }
  ]
}
