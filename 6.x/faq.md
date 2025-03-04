# Frequently Asked Questions

---


<a name="licensing"></a>
## Licensing

<a name="no-license-needed-on-localhost"></a>
### Do I need a license to test Backpack?

You don't need a license code AT ALL. Go ahead and install Backpack CRUD on your machine - it's free and open-source, released under the MIT License.

You only need to pay if you want the extra features provided by our premium add-ons (e.g. [Backpack PRO](https://backpackforlaravel.com/pricing) and [Backpack DevTools](https://backpackforlaravel.com/products/devtools)). That's it.


<a name="licese-for-staging-domain"></a>
### Do I need a license to put a PRO project on a testing domain?

No:
- when you purchase [Backpack PRO for Unlimited Projects](https://backpackforlaravel.com/products/pro-for-unlimited-projects), you can use it on any number of domains, subdomains and IPs (that's why it's called unlimited);
- when you purchase [Backpack PRO for One Project](https://backpackforlaravel.com/products/pro-for-one-project), you get the right to use it on one MAIN domain, but also on as many staging/test/beta domains or subdomains as you need; if someone from our team contacts you, then you can explain, it's perfectly reasonable to have test instances - we know how it goes;


<a name="backpack-license-for-open-source-projects"></a>
### Can I use Backpack to create an open-source project?

Yes you can! Use [Backpack CRUD v6](https://github.com/laravel-backpack/crud), which is free and open-source, released under the MIT License.


<a name="backpack-pro-for-open-source-projects"></a>
### Can I use Backpack PRO in an open-source project?

In short - no, you cannot. Please use [Backpack CRUD v6](https://github.com/laravel-backpack/crud) instead, which is free and open-source, released under the MIT License.

Backpack PRO is a closed-source add-on, which requires payment in order to receive an access token. If you did include `backpack/pro` as a dependency in your open-source software, then your software would no longer be open-source. Everybody who installed your project/package would need to pay for Backpack PRO to get access to it.


<a name="backpack-pro-for-non-commercial-projects"></a>
### Can I get Backpack PRO for free to use in a non-commercial project?

No - we're no longer giving away free licenses. But we _have_ released Backpack CRUD v5 and v6 under the MIT License, which means it's free and open-source. It has fewer features, but you can do absolutely do anything you want with it.

<a name="backpack-pro-discount-for-university"></a>
### Can I get a discount for university use?

Yes, if you are a teacher at highschool or university and want to use Backpack to teach your students how to code, we're happy to give you a discount for our MULTI-PROJECT and EVERYTHING plans, so your students can quickly learn PHP, Laravel and Backpack. Reach out to us from your .edu email (or provide other proof). For this use case, please make sure to "refresh" your token every year, to prevent misuse.

<a name="backpack-pro-discount-for-backlink"></a>
### Can I get a discount for a backlink?

Yes, but only for your second/third purchases. Before your license expires, you will receive an email offering you 20% discount if you include a backlink from your website to ours. Reply to that email with a link, and our team will help you get the discount.

To get the 20% discount:
- include a backlink from your MAIN WEBSITE (not from your admin panel), to our main website: `https://backpackforlaravel.com?utm_source=customer_ref`; most people add a link to their footer; here are a few ideas: `Admin Panel by Backpack`, `Backoffice by Backpack`, `CMS Powered by Backpack`, `ERP Powered by Backpack`, `Powered By Backpack`;
- make sure the link is `dofollow` (regular link);
- make sure to keep the link there for 12 months;

<a name="Installation"></a>
## Installation


<a name="how-do-i-update-backpack"></a>
### How do I update Backpack to the latest non-breaking version?

Run **`composer update`** on your project to update the dependencies given your version constrains in `composer.json`. If you want to update a specific package, you can run **`composer update backpack/crud`** for example to only update `backpack/crud` and it's dependencies.

If you have your assets cached you can run `php artisan basset:clear` to clear the cache too. You can manually rebuild the asset cache if necessary with `php artisan basset:cache`. We do recommend you keep basset disabled on localhost while developing. 

Some packages may require you to run additional commands to update the database schema or publish new assets. Please refer to the package documentation or upgrade guide for more information.

<a name="how-do-i-uninstall-backpack"></a>
### How do I uninstall Backpack from my project?

You can remove Backpack from your project pretty easily, if you decide to stop using it. You just have to do the opposite of the installation process:

```bash
# delete the files Backpack has placed inside your application
rm -rf app/Http/Middleware/CheckIfAdmin.php
rm -rf config/backpack
rm -rf config/gravatar.php
rm -rf resources/views/vendor/backpack
rm -rf routes/backpack

# delete any CrudControllers you've created, so MAYBE:
rm -rf app/Http/Controllers/Admin

# delete any Requests you've created for your CrudControllers.
# MAKE SURE YOU DON'T NEED ANYTHING IN THIS DIRECTORY ANYMORE.
# You might have OTHER requests that are not Backpack-related.
rm -rf app/Http/Requests

# (MUST) remove other Backpack packages that you are using, like PRO, Editable Columns, DevTools etc:
composer remove --dev backpack/devtools
composer remove backpack/pro
composer remove backpack/editable-columns

etc...

# After everything related to Backpack is deleted, just need to delete the crud!
composer remove backpack/crud

```

That's it! If you've decided NOT to use Backpack, we'd be super grateful if you could <a href="mailto:hello@backpackforlaravel.com?subject=Why%20I%20decided%20against%20using%20Backpack&body=Hey%20there%20Backpack%20team%2C%0D%0A%0D%0AHopefully%20my%20experience%20will%20help%20you%20take%20Backpack%20in%20a%20direction%20where%20it'll%20be%20a%20better%20fit%20for%20me%20in%20the%20future.%0D%0A%0D%0AI've%20decided%20to%20NOT%20use%20Backpack%20in%20my%20project%20because...">send us an email</a> telling us WHY you decided not to use Backpack, or why it didn't fit your project. It might help us take Backpack in a different direction, a direction where you might want to use it again. Thank you 🙏


<a name="errors-when-installing-paid-add-ons"></a>
### Errors when installing paid add-ons

When installing our [paid add-ons](https://backpackforlaravel.com/addons):
- Composer will add our private repository (`repo.backpackforlaravel.com`) to your `composer.json` file;
- Composer will try to download the `dist` version of the package from there;
    - if successful, you're good;
    - if the `dist` version fails to download, Composer will throw an error (with an HTTP code like 402); then Composer will try to download the `source` version of the package straight from our Github repo; that will 100% fail, because you do NOT have access to our private Github repo; to rephrase, you don't have access to the `source`, only to the `dist` version;

Unfortunately, we cannot customize the errors that Composer throws, so the error text might be confusing. Please take a look at the HTTP error code shown in the error to understand what happened:
- 400 Error - Bad Request - user and password do not match; please check your auth credentials;
- 401 Error - Unauthorized - no token username or password; please check your auth credentials;
- 402 Error - Payment Required - you are trying to download a version newer than you have access to; our system will send you an email with clear instructions on what to do to require the latest version you have access to; you can also check the latest version you have access to in your Backpack account, and require that version specifically; alternatively, please purchase the same product again to gain access to more updates, then it will work again; 
- 404 Error - Not Found - the package that you are trying to download does not exist;
- 429 Error - Too Many Requests - our server has received too many requests from your IP address; please wait one minute and try again;

If you still can't figure it out, please [open a new discussion in our Community Forum](https://github.com/Laravel-Backpack/community-forum/discussions/categories/q-a-help). Please make sure to:
- mention the steps you have followed to get there (e.g. `composer require backpack/pro`, `php artisan backpack:require:pro` etc.);
- include a screenshot of the console output, so we can understand what happened;
- cross out any personal data (e.g. token username or password); 

<a name="how-do-i-deploy-backpack-to-production"></a>
### How do I deploy Backpack to production?

Deploying a Laravel+Backpack project to production isn't very different from deploying a normal Laravel project to production. You only need to account for Basset, the system in Backpack that publishes the CSS and JS assets your admin panel needs. 

That being said, here's a detailed step-by-step guide to deploying a Backpack project to production, that should work for most production servers: 

1. Local Preparations
Before deploying your application, make sure your development environment is in order:
- Update dependencies:
```
composer install --no-dev --optimize-autoloader
npm install && npm run prod
```
- Configure your .env file for production. This file should not be included in version control (git), but make sure to have a copy with production settings.

2. Configure the Production Server
Ensure your production server meets the following requirements:

Web Server: Nginx or Apache.
PHP: Version compatible with your Laravel version.
Database: MySQL, PostgreSQL, etc.
Composer: Installed globally.
Node.js and npm: If you are using asset compilation with Laravel Mix.

3. Deploy the Code
There are several ways to deploy code to production, here’s one method using Git:
- Clone your repository on the server:
```
git clone https://github.com/your_user/your_repository.git
cd your_repository
```
- Copy your local .env file to the production environment and adjust the settings accordingly.
```
APP_ENV=production
APP_URL=https://MY_DOMAIN.COM #This need to be correctly set for Basset
```

4. Install Dependencies
Install Composer and npm dependencies:
```
composer install --no-dev --optimize-autoloader
npm install && npm run prod
```

5. Configure the Application
Make the necessary configurations for the application:
- Generate the application key:
```
php artisan key:generate
```
- Set permissions:
```
sudo chown -R www-data:www-data storage
sudo chown -R www-data:www-data bootstrap/cache
sudo chmod -R 775 storage
sudo chmod -R 775 bootstrap/cache
```
- Run database migrations:
```
php artisan migrate --force
```
- Optimize configuration:
```
php artisan basset:clear
php artisan basset:cache
php artisan optimize:clear
php artisan optimize
```

6. Configure the Web Server
Set up your web server (Nginx or Apache) to point to the public directory of your Laravel application. Here’s an example Nginx configuration:
```
server {
    listen 80;
    server_name your_domain.com;
    root /path/to/your/project/public;

    index index.php index.html;

    location / {
        try_files $uri $uri/ /index.php?$query_string;
    }

    location ~ \.php$ {
        include snippets/fastcgi-php.conf;
        fastcgi_pass unix:/var/run/php/php8.2-fpm.sock;
        fastcgi_param SCRIPT_FILENAME $document_root$fastcgi_script_name;
        include fastcgi_params;
    }

    location ~ /\.ht {
        deny all;
    }
}
```

7. Configure Cron Jobs
If your Laravel application uses scheduled tasks, add the following line to your crontab:
```
* * * * * cd /path/to/your/project && php artisan schedule:run >> /dev/null 2>&1
```

8. Monitor and Maintain
Monitor your application in production:
- Logs: Check logs in storage/logs/laravel.log.
- Services: Ensure services like queue workers are running properly.

9. Optimize for Production
Perform additional optimizations if necessary:
- Optimize Autoload:
```
composer dump-autoload --optimize
```
- Disable Debug in .env:
```
APP_DEBUG=false
```
- Clean & Set config cache
```
php artisan config:clear
php artisan config:cache
```
