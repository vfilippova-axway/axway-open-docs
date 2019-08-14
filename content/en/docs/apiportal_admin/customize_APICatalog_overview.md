{"title":"Customize API Catalog","linkTitle":"Customize API Catalog","weight":"3","date":"2019-07-30","description":""}

## Customize API Catalog

You can customize how APIs are displayed in the API Catalog view of API Portal, and you can customize what actions your API consumers can perform on APIs.

You can customize the following:

- Display APIs in a list or tile view.
- Show or hide the button that enables users to download API definitions. The default is shown.
- Show or hide the button that enables users to download client SDKs. The default is hidden.
- Hide APIs associated with specified tags.
- Show only APIs associated with specified tags. For more details on tags, see Group APIs with tags.
- Show or hide the button that enables users to try out an API. You can show the button for all users, for authenticated users only, or hide it completely. The default is shown for all users.
- Display REST API details using the Swagger.io or AMPLIFY Swagger UI rendering tools. The default is AMPLIFY. SOAP APIs are always displayed using Swagger.io.
- Show or hide Nickname column while using AMPLIFY Swagger UI rendering tool.
- Display REST API details using a colorful or colorless scheme when using the AMPLIFY rendering tool. You can also change the method colors when using the colorful scheme.

## Customize API Catalog settings

To change the API Catalog settings:

1. Log in to the Joomla! Administrator Interface (JAI) (`https://<API Portal_host>/administrator`).
1. Click **Menus > Main Menu**.
1. Click **APIs**.
1. Click the **API Catalog** tab.

   ![Customize API catalog](/Images/APIPortal/jai_customize_api_catalog.png)
   
1. Change the settings as required and click **Save & Close**.

## Customize source of API descriptions

You can customize API Portal to show summaries instead of descriptions for APIs to give the app developer a quicker summary view of what the API is about instead of a long description. Using description can also have a performance impact, so it is best to use summary to improve the performance of the API Catalog view.

To change the settings:

1. In the Joomla! Administrator Interface (JAI), click **Components > API Portal > Additional Settings**.
2. In the **API Information Source** field, select `Description` or `Summary`.
3. Click **Save**.

## Customize page title or summary
You can customize the API Catalog page title, the summary text, or both.

1. In JAI, click **Menus > Main Menu**.
2. Select **APIs**, and go to the **Page Display** tab.
3. In **Masthead Title**, enter the new page title. If you leave this empty, the default title is used.
4. In **Masthead Slogan**, enter the new summary. If you leave this empty, the default text is used.
5. Click **Save & Close**.

## Group APIs with tags
You can add tags to APIs in API Manager and use them to split your API Catalog into smaller subsets. For example, you can create multiple themed API groups based on your developer communities.

For more details on adding tags to APIs, see the [API Manager User Guide](/bundle/APIManager_77_APIMgmtGuide_allOS_en_HTML5/) .

To create a dedicated API Catalog for a subset of tagged APIs, do the following:

1. Log in to Joomla! Administrator Interface (JAI).
1. Click **Menus > Main Menu > Add New Menu Item**.
1. Enter a menu title for the new API Catalog.
1. In **Menu Item Type**, click **Select > API Portal > API Catalog Page**.
1. Set **Access** to the level you want, and ensure that **Status** is set to `Published`.
1. In **Ordering**, select where in the main menu the new API Catalog appears. The menu item is placed after the item you select here.
  To access all your API Catalogs under the **APIs** menu item rather than additional menu items, set **Parent Item** to **APIs**.
1. On the **API Catalog** tab, in the **Only list APIs with tag**, enter the tags to include in this API Catalog.
1. On the **Page Display** tab, change the page title and summary text if you want. For more details, see [Change the page title or summary](customize_APICatalog_view.htm#Change).
1.. Click **Save & Close**.

Your themed API Catalog is now ready, and you can see it in your API Portal.

You can also choose to use some tags as an internal tool, and hide them from the API consumers. To hide tags, On the **API Catalog** tab, in **Hide tags**, enter the tags to hide.

## Create tags with wildcards

In both **Only list APIs with tag** and **Hide tags** options on **API Catalog** tab, you can add tags using the `*` and `+` wildcards. This is helpful when you want, for example, list only development APIs in one API Catalog and production APIs in another. In this case you can filter them using wildcards as follows: `*dev*` will list APIs which contain `dev` somewhere in the tag, for example, `financial_development` and `development` tags. Or, to hide all tags which start with `test` and end with any other letter, for example, `test` or `tests`, you can do `test+`.
