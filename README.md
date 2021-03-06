#  Retrieve data from Google Analytics

[![Latest Version](https://img.shields.io/github/release/spatie/laravel-analytics.svg?style=flat-square)](https://github.com/spatie/laravel-analytics/releases)
[![Software License](https://img.shields.io/badge/license-MIT-brightgreen.svg?style=flat-square)](LICENSE.md)
[![Build Status](https://img.shields.io/travis/spatie/laravel-analytics/master.svg?style=flat-square)](https://travis-ci.org/spatie/laravel-analytics)
[![Quality Score](https://img.shields.io/scrutinizer/g/spatie/laravel-analytics.svg?style=flat-square)](https://scrutinizer-ci.com/g/spatie/laravel-analytics)
[![SensioLabsInsight](https://img.shields.io/sensiolabs/i/22e652a0-01c0-4b8c-9ada-c9ba5cba17c7.svg?style=flat-square)](https://insight.sensiolabs.com/projects/22e652a0-01c0-4b8c-9ada-c9ba5cba17c7)
[![StyleCI](https://styleci.io/repos/32067087/shield)](https://styleci.io/repos/32067087)
[![Total Downloads](https://img.shields.io/packagist/dt/spatie/laravel-analytics.svg?style=flat-square)](https://packagist.org/packages/spatie/laravel-analytics)

Using this package you can easily retrieve data from Google Analytics.

Here are a few examples of the provided methods:

```php
use Spatie\Analytics\Period;

//fetch the most visited pages for today and the past week
Analytics::fetchMostVisitedPages(Period::days(7));

//fetch visitors and page views for the past week
Analytics::fetchVisitorsAndPageViews(Period::days(7));
```

Most methods will return an `\Illuminate\Support\Collection` object containing the results.

Spatie is a webdesign agency in Antwerp, Belgium. You'll find an overview of all our open source projects [on our website](https://spatie.be/opensource).

## Postcardware

You're free to use this package (it's [MIT-licensed](LICENSE.md)), but if it makes it to your production environment you are required to send us a postcard from your hometown, mentioning which of our package(s) you are using.

Our address is: Spatie, Samberstraat 69D, 2060 Antwerp, Belgium.

The best postcards will get published on the open source page on our website.

## Install

This package can be installed through Composer.

``` bash
composer require spatie/laravel-analytics
```

You must install this service provider.

```php
// config/app.php
'providers' => [
    ...
    Spatie\Analytics\AnalyticsServiceProvider::class,
    ...
];
```

This package also comes with a facade, which provides an easy way to call the the class.

```php
// config/app.php
'aliases' => [
    ...
    'Analytics' => Spatie\Analytics\AnalyticsFacade::class,
    ...
];
```

You can publish the config file of this package with this command:

``` bash
php artisan vendor:publish --provider="Spatie\Analytics\AnalyticsServiceProvider"
```

The following config file will be published in `config/laravel-analytics.php`

```php

return [ 
    /*
     * The view id of which you want to display data.
     */
    'view_id' => env('ANALYTICS_VIEW_ID'),

    /*
     * Path to the json file with service account credentials. Take a look at the README of this package
     * to learn how to get this file.
     */
    'service_account_credentials_json' => storage_path('app/laravel-google-analytics/service-account-credentials.json'),

    /*
     * The amount of minutes the Google API responses will be cached.
     * If you set this to zero, the responses won't be cached at all.
     */
    'cache_lifetime_in_minutes' => 60 * 24,
    
    /*
     * The directory where the underlying Google_Client will store it's cache files.
     */
    'cache_location' => storage_path('app/laravel-google-analytics/google-cache/'),
];

```

## How to obtain the credentials to communicate with Google Analytics

### Getting credentials

The first thing you’ll need to do is to get some credentials to use Google API’s. I’m assuming that you’ve already created a Google account and are signed in. Head over to [Google API’s site](https://console.developers.google.com/apis) and click "Select a project" in the header.

![1](https://spatie.github.io/laravel-analytics/v2/1.jpg)

Next up we must specify which API’s the project may consume. In the list of available API’s click "Google Analytics API". On the next screen click "Enable".

![2](https://spatie.github.io/laravel-analytics/v2/2.jpg)

Now that you’ve created a project that has access to the Analytics API it’s time to download a file with these credentials. Click "Credentials" in the sidebar. You’ll want to create a "Service account key".

![3](https://spatie.github.io/laravel-analytics/v2/3.jpg)

On the next screen you can give the service account a name. You can name it anything you’d like. In the service account id you’ll see an email address. We’ll use this email address later on in this guide. Select "JSON" as the key type and click "Create" to download the JSON file.

![4](https://spatie.github.io/laravel-analytics/v2/4.jpg)

Save the json inside your Laravel project at the location specified in the `service_account_credentials_json` key of the config file of this package. Because the json file contains potentially sensitive information I don't recommend committing it to your git repository.

### Granting permissions to your Analytics property

I'm assuming that you've already created a Analytics account on the [Analytics site](https://analytics.google.com/analytics). Go to "User management" in the Admin-section of the property.

![5](https://spatie.github.io/laravel-analytics/v2/5.jpg)

On this screen you can grant access to the email address found in the `client_email` key from the json file you download in the previous step. Read only access is enough.

![6](https://spatie.github.io/laravel-analytics/v2/6.jpg)
 
### Getting the view id
 
The last thing you'll have to do is fill in the `view_id` in the config file. You can get the right value on the [Analytics site](https://analytics.google.com/analytics). Go to "View setting" in the Admin-section of the property.

![7](https://spatie.github.io/laravel-analytics/v2/7.jpg)

You'll need the `View ID` displayed there.

![8](https://spatie.github.io/laravel-analytics/v2/8.jpg)

## Usage

When the installation is done you can easily retrieve Analytics data. Nearly all methods will return an `Illuminate\Support\Collection`-instance.


Here is an example to retrieve visitors and pageview data for the current day and the last seven days.
```php
$analyticsData = Analytics::fetchVisitorsAndPageViews(Period::days(7));
```

`$analyticsData` is a `Collection` in which each item is an array that holds keys `date`, `visitors` and `pageViews`

If you want to have more control over the period you want to fetch data for, you can pass a `startDate` and an `endDate` to the period object.

```php
$startDate = Carbon::now()->subYear();
$endDate = Carbon::now();

Period::create($startDate, $endDate);
```

## Provided methods

### Visitors and pageviews

```php
public function fetchVisitorsAndPageViews(Period $period): Collection
```

The function returns a `Collection` in which each item is an array that holds keys `date`, `visitors`, `pageTitle` and `pageViews`.

### Total visitors and pageviews

```php
public function fetchTotalVisitorsAndPageViews(Period $period): Collection
```

The function returns a `Collection` in which each item is an array that holds keys `date`, `visitors`, and `pageViews`.

### Most visited pages

```php
public function fetchMostVisitedPages(Period $period, int $maxResults = 20): Collection
```

The function returns a `Collection` in which each item is an array that holds keys `url`, `pageTitle` and `pageViews`.

### Top referrers

```php
public function fetchTopReferrers(Period $period, int $maxResults = 20): Collection
```

The function returns a `Collection` in which each item is an array that holds keys `url` and `pageViews`.

### Top browsers

```php
public function fetchTopBrowsers(Period $period, int $maxResults = 10): Collection
```

The function returns a `Collection` in which each item is an array that holds keys `browser` and `sessions`.

### All other Google Analytics queries

To perform all other queries on the Google Analytics resource use `performQuery`.  [Google's Core Reporting API](https://developers.google.com/analytics/devguides/reporting/core/v3/common-queries) provides more information on which metrics and dimensions might be used. 

```php
public function performQuery(Period $period, string $metrics, array $others = [])
```

You can get access to the underlying `Google_Service_Analytics` object:

```php
Analytics::getAnalyticsService();
```

## Testing

Run the tests with:

``` bash
vendor/bin/phpunit
```

## Contributing

Please see [CONTRIBUTING](CONTRIBUTING.md) for details.

## Security

If you discover any security related issues, please email freek@spatie.be instead of using the issue tracker.

## Credits

- [Freek Van der Herten](https://github.com/freekmurze)
- [All Contributors](../../contributors)

## About Spatie
Spatie is a webdesign agency in Antwerp, Belgium. You'll find an overview of all our open source projects [on our website](https://spatie.be/opensource).

## License

The MIT License (MIT). Please see [License File](LICENSE.md) for more information.
