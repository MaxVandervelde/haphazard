Haphazard
=========

Haphazard is a library extension to Symfony's WebTestCase that provides a quick
and dirty method to functional test basic pages.

Installation
------------

You can install Haphazard through composer:

    composer.phar require ink/haphazard

Usage
-----

### Simple page load test

When all you want to test is that the page at *least* loads properly, you can
use the `HaphazardTestCase::assertGet()` or `HaphazardTestCase::assertPost()`
methods. This will create a web client and assert that a response code of 200
is returned for the page.

    use Ink\Haphazard\TestCase\HaphazardTestCase;

    class ProductControllerTest extends HaphazardTestCase
    {
        /**
         * Test Index Action
         */
        public function testIndexAction()
        {
            $this->assertGet('index');
        }

        /**
         * Test Post Action
         */
         public function testCreatePostAction()
         {
             $this->assertPost('create');
         }
    }

### Page with parameters

If the page you're testing requires parameters, you can pass those in as well.

    /**
     * Test View Action
     */
    public function testViewAction()
    {
        $this->assertGet('product-view', ['productId' => 1]);
    }

    /**
     * Test Edit Action
     */
     public function testEditAction()
     {
         $this->assertPost('product-edit', ['productId' => 1]);
     }

### Page with a Post body

If you need to send additional parameters along with your POST request, you can
send them along.

    /**
     * Test Edit Action
     */
     public function testEditAction()
     {
         $this->assertPost('product-edit', ['productId' => 1], ['product-name' => 'Acme Product']);
     }

### Different Status codes

Occasionally you will want to test that pages return a different status code,
for example a 403 / Forbidden status code when an anonymous user should *not*
be able to access a given page, or a 302 when the page redirects.

    /**
     * Test Edit Action
     */
    public function testEditAction()
    {
        $this->assertGet('product-edit', ['productId' => 1], 403);
    }

    /**
     * Test Edit Action
     */
     public function testEditAction()
     {
         $this->assertPost('product-edit', ['productId' => 1], ['product-name' => 'Acme Product'], 302);
     }

### Spoof Authentication Role

In order to effectively test that pages are open / closed to the correct users,
this library provides an easy way to make assertions using a specified role.

    /**
     * Test Edit Action
     */
    public function testEditAction()
    {
        // Allow our Admin role
        $user = new User();
        $this->login($user);
        $this->assertGet('product-edit', ['productId' => 1], 200);

        // Disallow Anonymous users
        $this->refreshClient();
        $this->assertGet('product-edit', ['productId' => 1], 403);
    }
