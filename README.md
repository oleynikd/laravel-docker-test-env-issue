There's a fresh Laravel installation inside a [blog folder](https://github.com/oleynikd/laravel-docker-test-env-issue/tree/master/blog). I have only removed the default .env file and added:
```
dd(config('app.env'));
```
to a [CreatesApplication.php](https://github.com/oleynikd/laravel-docker-test-env-issue/blob/71b9519e8f635cd949bf59747f31fd32ad4e91e4/blog/tests/CreatesApplication.php#L21)

Run 
```bash
docker-compose up
```
or if you don't have a compose:
```bash
docker run --env APP_ENV=docker -w /src -v "$PWD/blog":/src php:7.4 php artisan test
```
and you will get:
```bash
php_1  |    RUNS  Tests\Unit\ExampleTest
php_1  |   • basic test
php_1  |
php_1  |   Tests:  2 pending
php_1  |
php_1  |    PASS  Tests\Unit\ExampleTest
php_1  |   ✓ basic test
php_1  |
php_1  |    RUNS  Tests\Feature\ExampleTest
php_1  |   • basic test
php_1  |
php_1  |   Tests:  1 passed, 1 pending
php_1  | "docker"
php_1 exited with code 1
```

Application environment is set to a `"docker"` which was passed with [docker --env](https://docs.docker.com/engine/reference/commandline/run/#set-environment-variables--e---env---env-file) argument. However, the expected behavior and response is `"testing"`.

This test demonstrates that env variables from a [phpunit.xml](https://github.com/oleynikd/laravel-docker-test-env-issue/blob/master/blog/phpunit.xml) are not loaded or are overrode, therefore, the `DB_CONNECTION` and `DB_DATABASE` will also be overridden by `docker --env`.

This issue leads to a complete database erase if you are using a `RefreshDatabase` trait because [usingInMemoryDatabase()](https://github.com/laravel/framework/blob/4110965660ffc459206a5f1e5c3b40354347cd08/src/Illuminate/Foundation/Testing/RefreshDatabase.php#L26) will return `false` and than [refreshTestDatabase()](https://github.com/laravel/framework/blob/4110965660ffc459206a5f1e5c3b40354347cd08/src/Illuminate/Foundation/Testing/RefreshDatabase.php#L50) will call a `migrate:fresh`...



