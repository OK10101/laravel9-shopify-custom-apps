# <h1> Laravel 9 Shopify PHP + MySQL Custom Apps XLSX Exports
This custom apps is able to be install in Shopify Shop.
Build with PHP, Laravel 9, MySQL

![Alt text](public/images/github/home.png?raw=true "Home")


* List order/orders ONLY.
![Alt text](public/images/github/order_list1.png?raw=true "Order List")

* Show individual order with Product, Items, and variants
![Alt text](public/images/github/single_order.png?raw=true "Single Order")

* Export "Order List" to Excel XLSX format. (Can be extend for csv, xls, ods, pdf, html) 
    * Used package <a href="https://github.com/maatwebsite/Laravel-Excel">Maatwebsite/Laravel-Excel</a>
    * ![Alt text](public/images/github/export_excel.png?raw=true "Export Excel")

* Authentication & installation for shops once the secret key set correctly, refer to "Installation" section
  
# <h2>Installation
- Clone this repo from GitHub
## <h3> Prerequisite
   * Composer version 2
   * PHP 8.0 >
   * MySQL database
## <h3> Composer install
* Run <i>composer install</i> to install necessary packages
* After running <i>composer install</i>, the two package above should have been installed.
## <h3> Mysql Database table setup
* Create a new database in your MySQL DB.
* Modify .env to accomodate new DB settings, example:
    ```php
    DB_CONNECTION=mysql
    DB_HOST=127.0.0.1
    DB_PORT=8889
    DB_DATABASE=shopify_cust
    DB_USERNAME=root
    DB_PASSWORD=root
    ```
* Table setup has two options:
  1. Run <i>php artisan migrate</i>, to automatically create tables.
  2. Execute the content of .sql file in /database/sql/DB.sql to populate all tables.

## <h3> Shopify Key/Secret and webhooks
* Open file <i>config/shopify-app.php</i>.
* Key and Secret setup are in below two sectios. Replace the Key/Secret value.
    ```php
    /*
    |--------------------------------------------------------------------------
    | Shopify API Key
    |--------------------------------------------------------------------------
    |
    | This option is for the app's API key.
    |
    */

    'api_key' => env('SHOPIFY_API_KEY', 'ebb9f37de9b1253506d18036208702ba'),

    /*
    |--------------------------------------------------------------------------
    | Shopify API Secret
    |--------------------------------------------------------------------------
    |
    | This option is for the app's API secret.
    |
    */

    'api_secret' => env('SHOPIFY_API_SECRET', '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'),
    ```
* Update your Shopify Store app name
    ```php
    /*
    |--------------------------------------------------------------------------
    | Shopify App Name
    |--------------------------------------------------------------------------
    |
    | This option simply lets you display your app's name.
    |
    */

    'app_name' => env('SHOPIFY_APP_NAME', '<your-app-name>'),
    ```
* Next, edit <i>config/shopify-app.php</i>, section "Shopify Webhooks", replace the entry of "https://some-app.com" to your apps domain URL. Keep the portion of "/webhook/orders-create"
    ```php
    // ...
    'webhooks' => [
        [
            'topic' => 'orders/create',
            'address' => 'https://some-app.com/webhook/orders-create'
        ],
    ],
    //...
    ```
## <h3> Shopify Custom Apps Setup
- Redirection URLs:
    ```php
        https://some-app.com/authenticate
    ```
- Order Webhook setting:
    - Event: Order creation
    - Format: JSON
    - Webhook API version: 2022-1 (Latest)
    - URL has to fix to <i>webhook/orders-create</i>, example:
    ```php
        https://some-app.com/webhook/orders-create
    ```
# <h2> Common Errors & Fix
## <h3> Shopify Refuse to connect (LINUX, PHP, NGINX)

![Alt text](public/images/github/refuse_connect.png?raw=true "shopify refuse to connect")

- The browser inspect shows "Refused to display ‘URL’ in a frame because it set ‘X-Frame-Options’ to ‘deny’"

![Alt text](public/images/github/x-frame-deny.png?raw=true "x-frame deny")

### <h4> How to fix this issue.
1. Search Nginx Config for “X-Frame-Options”
    ```sh
    $ sudo grep -iRl "X-Frame-Options" /etc/nginx/
    ```
    - <h5> Output
    ```sh
        /etc/nginx/sites-available/shopify
        /etc/nginx/snippets/ssl-params.conf
    ```
2. In the output file, search for line:
    ```sh
        add_header X-Frame-Options DENY;
    ```
3. Either change this to SAME-ORIGIN or just comment out the line with a # sign and Nginx will default to SAME-ORIGIN. Both methods should produce the same result.
    ```sh
        add_header X-Frame-Options SAME-ORIGIN;
    ```
    - or
    ```sh
        #add_header X-Frame-Options DENY;
    ```
4. Test Nginx config.
    ```sh
        sudo nginx -t
    ```
5. Reload NGINX for setting to take effect.
    ```sh
        sudo service nginx reload
    ```
## <h3> Shopify install app connection stopped at home page and not installing.
- To fix this, make sure the database users table is empty or remove the installatio records for the same shop.

## <h3> "Unknown Error" after delete the apps and reinstall. App success installed.
- This issue still pending to find out.
- However, the app was successfully installed.

# <h2> Reference / How it works
## <h3> Router
- routes/web.php
## <h3> Controllers
- Shopify Order: app/Http/Controllers/ShopifyOrderController.php
## <h3> Models
- Shopify Order: app/Models/ShopifyOrder.php
- Shopify Order Fullfillment: app/Models/ShopifyOrderFulFillment.php
- Shopify Order Lineitems: app/Models/ShopifyOrderLineitems.php
## <h3> Views
- Shop landing page: resources/views/home/index.blade.php
- Order List: resources/views/order/index.blade.php
- Single order list: resources/views/order/show.blade.php
## <h3> Order Create webhook handle
- app/Jobs/OrderCrateJob.php
## <h3> Export xlsx 
- Controller: app/Http/Controllers/ShopifyOrderController.php
- Function
    ```php
    public function exportTablesToExcel(){
        return Excel::download(new OrderExport, 'Order.xlsx');
    }
    ```

# <h2> Demo
- Due to custom apps, demo is not available.

# <h2> Contributing
Pull requests are welcome. 

# <h2> TODO
- Order update webhook
- Fulfillment webhook

# <h2> License
[MIT](https://choosealicense.com/licenses/mit/)