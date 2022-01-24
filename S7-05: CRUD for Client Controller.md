# S7-05: CRUD for Client Controller

Welcome back!

We want to dive right back in and repeat the steps we took on the project features of our application for clients. We’re going to improve the ClientController store() and update() methods by creating a ClientRequest class to handle all our validation and authorizations.

1. > ~~Refactor TimeEntryController~~
2. > ~~Complete TimeEntryController CRUD methods~~
3. > Update and Complete CRUD methods in ~~ProjectController~~ and ClientController
4. > Complete and Cross off all project TODOs
5. > Remove any unwanted or unused code

Open App/Http/Controllers/ClientController.php

![Screen Shot 2022-01-06 at 8.20.37 AM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-06%20at%208.20.37%20AM.png)

### Index()

This looks good for now, moving on.

### Create()

As we did with the ProjectController, we just want to add a return to the view.

```php
public function create()
{
  return view('clients.create');
}
```

And the same for the edit() method. We want to use route model binding, but this controller seems to be setup to use $id, we're going to change that.

Change this:

```php
public function edit($id) { ... }
```

To this:

```php
public function edit(Client $client)
{
  return view('clients.edit',compact('client'));
}
```

And let’s add a ClientRequest class to handle our store() and update() methods in this controller.

```Bash
$ sail artisan make:request ClientRequest
```

![Screen Shot 2022-01-06 at 8.29.38 AM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-06%20at%208.29.38%20AM.png)

Open App/Http/Requests/ClientRequest.php

![Screen Shot 2022-01-10 at 2.30.07 PM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-10%20at%202.30.07%20PM.png)

Include Auth in the header

```php
use Auth;
```

We’ll do the same authorize() check on clients as we did on projects.

```php
public function authorize()
{
  return auth()->check();
}
```

And the rules() method should return the following:

```php
return [
  'name' => ['required', 'string'],
  'code' => ['required', 'string'],
  'phone' => ['nullable'],
  'address' => ['nullable'],
];
```

![Screen Shot 2022-01-10 at 2.42.01 PM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-10%20at%202.42.01%20PM.png)

Open the ClientController

Add the ClientRequest to the header

```php
use App\Http\Requests\ClientRequest;
```

### Store()

Update the store() method to include the ClientRequest and use the validated request data.

```php
public function store(ClientRequest $request)
{
  $client = Client::create($request->validated());
}
```

And again the same strategy for our update() method.

```php
public function update(ClientRequest $request, Client $client)
{
  $client->update($request->validated());
}
```

Great, now remember, we have to allow these columns for mass assignment in our Client model.

Open App/Models/Client.php

![Screen Shot 2022-01-10 at 2.54.57 PM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-10%20at%202.54.57%20PM.png)

Add the protected $fillable array of columns names we want to authorize for mass assignment.

```php
protected $fillable = ['name','code','phone','address'];
```

![Screen Shot 2022-01-10 at 2.56.16 PM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-10%20at%202.56.16%20PM.png)

Oh do you feel that? I sense a storm of red test results in the forecast.

```Bash
$ sail artisan test --filter=ClientTest
```

![Screen Shot 2022-01-10 at 3.04.31 PM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-10%20at%203.04.31%20PM.png)

Let’s fix those reds right now!

Open up tests/Feature/ClientTest.php

![Screen Shot 2022-01-10 at 3.05.28 PM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-10%20at%203.05.28%20PM.png)

We’ve seen this problem before, our authorized users are not being used in this test. let’s do some cleanup of our tests!

We’re going to use the user and role models, so include them in the header.

```php
use App\Models\User;
use App\Models\Role;
```

And now let’s create that setup() method so our tests can use the same user.

```php
public function setUp(): void
{
  parent::setUp();
  $this->artisan('db:seed --class=RoleSeeder');
  $this->user = User::factory()->create();
  $this->user->roles()->attach(Role::IS_USER);
  $this->owner = User::factory()->create();
  $this->owner->roles()->attach(Role::IS_OWNER);
}
```

I’m going to repurpose the `test_user_can_create_a_client(`) method for owners like I did with the project test.

```php
public function test_owner_can_create_a_client()
{
  $response = $this->actingAs($this->owner)->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
  $this->assertTrue(Client::all()->count() == 1);
}
```

![Screen Shot 2022-01-10 at 3.11.03 PM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-10%20at%203.11.03%20PM.png)

Let’s add the test to check if a user (with user role) cannot create a client.

```php
public function test_user_can_not_create_a_client()
{
  $response = $this->actingAs($this->user)->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
  $this->assertTrue(Client::all()->count() == 0);
}
```

And, let’s remove `test_user_can_view_a_client()` and `test_user_can_view_client_index_page()` methods, for the same reason we removed the similar on for projects.

```php
// REMOVE THIS METHOD
public function test_user_can_view_a_client()
{ 
  $response = $this->post('/clients', ['name' => 'ABC Company', 'code' => 'ABCCO']);
  $clients = Client::all();
  
  $this->assertTrue($clients->count() == 1);
  
  $client = $clients->first();  
  $response = $this->get('/clients/'.$client->id);
  $response->assertStatus(200);
  $response->assertSee($client->code);
}
```

```php
// REMOVE THIS METHOD
public function test_user_can_view_client_index_page() 
{
  $clients = Client::factory(3)->create();
  $clients = Client::all();
  
  $this->assertTrue($clients->count() == 3);
  
  $response = $this->get('/clients');
  $response->assertStatus(200);

  $firstClient = $clients->first();
  $lastClient = $clients->last();

  $response->assertSee($firstClient->code);
  $response->assertSee($lastClient->code);
}
```

And now we can duplicate the test_user_can_update_a_client() and have two tests, one for owners which can update a client, and one for users who cannot.

```php
public function test_owner_can_update_a_client()
{
  $client = Client::factory()->create(['name' => 'ABC Company', 'code' => 'ABCCO']);
  $this->assertTrue(Client::all()->count() == 1);
  $this->assertDatabaseHas('clients', ['name' => $client->name]);

  $response = $this->actingAs($this->owner)->put('/clients/'.$client->id, ['name' => 'ABC Company Updated', 'code' => 'ABCCO']);
  $this->assertDatabaseHas('clients', ['name' => 'ABC Company Updated']);
}
```

And it’s counterpart:

```php
public function test_user_can_not_update_a_client()
{
  $client = Client::factory()->create(['name' => 'ABC Company', 'code' => 'ABCCO']);
  $this->assertTrue(Client::all()->count() == 1);
  $this->assertDatabaseHas('clients', ['name' => $client->name]);

  $response = $this->actingAs($this->user)->put('/clients/'.$client->id, ['name' => 'ABC Company Updated', 'code' => 'ABCCO']);
  $this->assertDatabaseHas('clients', ['name' => $client->name]);
}
```

And for the delete test, we’ll create two tests for that; one for owners, who can delete, and one for users who cannot.

```php
public function test_owner_can_delete_a_client()
{
  $client = Client::factory()->create();
  $this->assertTrue(Client::all()->count() == 1);
  
  $response = $this->actingAs($this->owner)->delete('/clients/'.$client->id);
  $this->assertTrue(Client::all()->count() == 0);
}
```

```php
public function test_user_can_not_delete_a_client()
{
  $client = Client::factory()->create();
  $this->assertTrue(Client::all()->count() == 1);
  
  $response = $this->actingAs($this->user)->delete('/clients/'.$client->id);
  $this->assertTrue(Client::all()->count() == 1);
}
```

Finally, run our test to verify that all the owner tests pass and (just like projects) we should expect to see the user tests fail.

```Bash
$ sail artisan test --filter=ClientTest
```

![Screen Shot 2022-01-10 at 3.32.51 PM.png](S7-05:%20CRUD%20for%20Client%20Controller.assets/Screen%20Shot%202022-01-10%20at%203.32.51%20PM.png)

### Great, so what now?

Well, our tests are not passing so we are not in a place where we can merge our changes into the main branch, but we can commit them, so let’s do that now.

```Bash
$ git status
$ git add .
$ git commit -m "adds client request, refactors client controller and tests"
```

