# jQuery Ajaxs Plugin
Send jQuery ajax requests one by one or pararell

```javascript
//two ajaxs requests one by one
$.ajaxs([{
		url: '/task/1',
		data: 'myparam=testing',
		method: 'post'
	},{
		url: '/task/2',
		method: 'post'
	}], {
		maxParallel: 1
	})
	.done(function() {
		console.log('Both task done one by one!');
	})
	.fail(function(jqXHR, textStatus, errorThrown) {
		console.log('Something was wrong :( ', errorThrown);
	});
```

Methods
-------
Methods library

* * *

### $.ajaxs(ajaxs [, settings]): AjaxsPromise
Call jQuery ajaxs requests one by one or parallel

#### Arguments
1. `ajaxs` (**Object[]**) An array of [jQuery ajax request settings](http://api.jquery.com/jquery.ajax/)
2. `settings` (**Object: optional**) jQuery [Ajaxs Settings](#settings)

#### Returns
**[AjaxsPromise](#ajaxspromise-methods)**: The jQuery promise object extended

* * *

### $.ajaxsManager(): AjaxsManager
Like a builder object to call $.ajaxs easily

#### Returns
**[AjaxsManager](#ajaxspromise-methods)**


Settings
-------
Ajaxs Settings

* * *

#### maxParallel

*type*: int  
*default*: 1  
How many requests can be processed simultaneously. Set 0 for no limit


AjaxsManager Methods
-------

* * *

### setMaxParallel(maxParallel): AjaxsManager
Set maxParallel setting

#### Arguments
1. `maxParallel` (**Int**)

#### Returns
**[AjaxsManager](#ajaxspromise-methods)**

* * *

### add(settings): AjaxsManager
Add one more request to call

#### Arguments
1. `settings` (**Object**) [jQuery ajax request settings](http://api.jquery.com/jquery.ajax/)

#### Returns
**[AjaxsManager](#ajaxspromise-methods)**

* * *

### abort(): AjaxsManager
Aborts the ajaxs

#### Returns
**[AjaxsManager](#ajaxspromise-methods)**

* * *

### run(): AjaxsManager
Run all the ajax requests added

#### Returns
**[AjaxsPromise](#ajaxspromise-methods)**: The jQuery promise object extended

# AjaxsPromise Methods
-------
A [jQuery Promise](http://api.jquery.com/Types/#Promise) object with done, fail, always, pipe... functions.

The deferred is resolved with the last resolve's ajax  
The deferred is rejected with the first reject's ajax
* * *

### abort(): AjaxsPromise
Aborts the ajaxs

#### Returns
**[AjaxsPromise](#ajaxspromise-methods)**

Examples
-------

### test.php

```php
<?php
if (isset($_GET['pos'])) {
    usleep(rand(300, 1000)*1000);
    echo "pos: ".$_GET['pos'];
    exit;
}
```

### jQuery Ajax requests

```javascript
var total = 4;
var ajaxs = [];
for (var i=0; i<total; i++) {
    ajaxs.push({
        url: 'test.php',
        data: {
            pos: i
        },
        success: function(data) {
            console.log('Done! '+data);
        }
    });
}
```

### One by one

```javascript
$.ajaxs(ajaxs, {
		maxParallel: 1
	})
    .done(function() {
        console.log('Finished OK');
    })
    .fail(function(jqXHR, textStatus, errorThrown) {
        console.error('Finished ERROR ', textStatus, errorThrown);
    });
```

##### Ouput:

```
Done! pos: 0  
Done! pos: 1  
Done! pos: 2  
Done! pos: 3  
Finished OK
```

### Parallel

```javascript
$.ajaxs(ajaxs,
	{
		maxParallel: 3
	})
    .done(function() {
        console.log('Finished OK');
    })
    .fail(function(jqXHR, textStatus, errorThrown) {
        console.error('Finished ERROR ', textStatus, errorThrown);
    });
```

##### Ouput:

```
Done! pos: 2  
Done! pos: 1  
Done! pos: 0  
Done! pos: 3  
Finished OK
```

### Manager

```javascript
$.ajaxsManager()
	.setMaxParallel(0)
	.add({
		url: 'test.php',
		data: {
			pos: 1
		}
	})
	.add({
		url: 'test.php',
		data: {
			pos: 2
		}
	})
	.run()
	.done(function() {
		console.log('Finish OK!');
	})
	.fail(function() {
		console.log('Finish Error ', arguments);
	});
```
