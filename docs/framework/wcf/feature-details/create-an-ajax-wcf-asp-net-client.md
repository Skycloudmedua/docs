---
title: "How to: Create an AJAX-Enabled WCF Service and an ASP.NET Client that Accesses the Service"
ms.date: 08/15/2018
ms.assetid: 95012df8-2a66-420d-944a-8afab261013e
---
# How to: Create an AJAX-Enabled WCF Service and an ASP.NET Client that Accesses the Service

This topic shows how to use Visual Studio to create an AJAX-enabled Windows Communication Foundation (WCF) service and an ASP.NET client that accesses the service.

## To create the ASP.NET client application

1. Open Visual Studio.

1. From the **File** menu, select **New** > **Project**

1. In the **New Project** dialog, expand the **Installed** > **Visual C#** > **Web** category, and then select **ASP.NET Web Application (.NET Framework)**.

1. Name the Project **SandwichServices** and click **OK**.

1. In the **New ASP.NET Web Application** dialog, select **Web Forms** and then select **OK**.

## To create the WCF AJAX-enabled service

1.  Right-click the SandwichServices project in **Solution Explorer** and select **Add** > **New Item**.

1. In the **Add New Item** dialog, expand the **Installed** > **Visual C#** > **Web** category, and then select the **WCF Service (AJAX-enabled)** template.

2.  Name the service **CostService** and then select **Add**.

3.  Open the *CostService.svc.cs* file.

4.  Specify the `Namespace` for <xref:System.ServiceModel.ServiceContractAttribute> as `SandwichService`:

    ```csharp
    namespace SandwichServices
    {
      [ServiceContract(Namespace = "SandwichServices")]
      [AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
       public class CostService
       {
         …
       }
     }
    ```

5.  Implement the operations in the service. Add the <xref:System.ServiceModel.OperationContractAttribute> to each of the operations to indicate that they are part of the contract.

   The following example implements a method that returns the cost of a given quantity of sandwiches:

    ```csharp
    public class CostService
    {
        [OperationContract]
        public double CostOfSandwiches(int quantity)
        {
            return 1.25 * quantity;
        }

    // Add more operations here and mark them with [OperationContract]
    }
    ```

## To configure the client to access the service

1.  Open the Default.aspx page and select the **Design** view.

2.  From the **View** menu, select **Toolbox**.

3.  Expand the **AJAX Extensions** node and drag and drop a **ScriptManager** on to the Default.aspx page.

4.  Right-click the **ScriptManager** and select **Properties**.

5.  Expand the **Services** collection in the **Properties** window to open up the **ServiceReference Collection Editor** window.

6.  Click **Add**, specify **CostService.svc** as the **Path** referenced, and click **OK**.

7.  Expand the **HTML** node in the **Toolbox** and drag and drop an **Input (Button)** on to the Default.aspx page.

8.  Right-click the **Button** and select **Properties**.

9. Change the **Value** field to **Price for 3 Sandwiches**.

10. Double-click the **Button** to access the JavaScript code.

11. Pass in the following JavaScript code within the **script** element.

    ```javascript
    function Button1_onclick() {
    var service = new SandwichServices.CostService();
    service.CostOfSandwiches(3, onSuccess, null, null);
    }

    function onSuccess(result){
    alert(result);
    }
    ```

## To access the service from the client

Press **Ctrl**+**F5** to launch the service and the Web client. Click the **Price for 3 Grilled Sandwiches** button to generate the expected output of "3.75".

## Example code

These examples contains the service code contained in the *WCFService.svc.cs* file and the client code contained in the *Default.aspx* file.

```csharp
// The service code contained in the CostService.svc.cs file.

using System;
using System.Linq;
using System.Runtime.Serialization;
using System.ServiceModel;
using System.ServiceModel.Activation;
using System.ServiceModel.Web;

namespace SandwichServices
{
    [ServiceContract(Namespace="SandwichServices")]
    [AspNetCompatibilityRequirements(RequirementsMode = AspNetCompatibilityRequirementsMode.Allowed)]
    public class CostService
    {
        // Add [WebGet] attribute to use HTTP GET
        [OperationContract]
        public double CostOfSandwiches(int quantity)
        {
            return 1.25 * quantity;
        }

        // Add more operations here and mark them with [OperationContract]
    }
}
```

```
// The code for hosting the service is contained in the CostService.svc file.

<%@ ServiceHost Language="C#" Debug="true" Service="SandwichServices.CostService" CodeBehind="CostService.svc.cs" %>

// The client code contained in the Default.aspx file.

<%@ Page Language="C#" AutoEventWireup="true" CodeBehind="Default.aspx.cs" Inherits="SandwichServices._Default" %>

<!DOCTYPE html PUBLIC "-//W3C//DTD XHTML 1.0 Transitional//EN" "http://www.w3.org/TR/xhtml1/DTD/xhtml1-transitional.dtd">

<html >
<head runat="server">
    <title>Untitled Page</title>
<script language="javascript" type="text/javascript">
// <!CDATA[

function Button1_onclick() {
var service = new SandwichServices.CostService();
service.CostOfSandwiches(3, onSuccess, null, null);
}

function onSuccess(result){
alert(result);
}

// ]]>
</script>
</head>
<body>
    <form id="form1" runat="server">
    <div>

    </div>
    <asp:ScriptManager ID="ScriptManager1" runat="server">
        <services>
            <asp:servicereference Path="CostService.svc" />
        </services>
    </asp:ScriptManager>
    </form>
    <p>
        <input id="Button1" type="button" value="Price for 3 Sandwiches" onclick="return Button1_onclick()" /></p>
</body>
</html>
```
