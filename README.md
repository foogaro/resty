#RESTY

##What is it? Or what is not?

Resty is a HTTP Server which is not a NGInX or Apache HTTP clone, and is not even a Tomcat HTTP Servlet Container.
Resty is just new approach for serving resources.
Flexible, extensible, just new.
It's an idea, maybe a bad idea... who konws.
It's written in Java... I might switch to Scala.


##How it works

You deploy ROAR files, which are Rest Oriented ARchive.
These files are unpacked as normal WAR or EAR in memory, not the HEAP though.

Inside the roar you write as many descriptor as you like, every descriptor file is intended to describe your resource.
Each descriptor can describe one or more resource. Each resource is described inside the descriptor file or in a different file, used just for the resource.

Here is an example of a descriptor file:


**_test.json_**

``` JSON
{"app":"test",
"root-path":"/myroar",
"resource":[
	{"name":"whoami","value":"Luigi"},
	{"name":"asl", "value":
		{"responder":"com.foogaro.resty.test.responder.Test",
		"return":"com.foogaro.resty.test.data.Person",
		"content-type":["application/xml","application/json"]
		}
	},
	{"import":"/my/cool/resource.json"}]
}
```


**_resource.json_**

``` JSON
{"name":"cities",
"value":
	{"responder":"com.foogaro.resty.adapter.responder.MySQL",
	"query":"SELECT * FROM TBL_USERS"
	"mapping":{"COLUMN_AGE":"age","COLUMN_SEX":"gender","COLUMN_LOCATION":"location"}
	"return":"com.foogaro.resty.test.data.Person",
	"content-type":["application/xml","application/json"]
	}
	
}
```

**_Person.java_**

``` Java
public class Person {
	private String name;
	private String gender;
	private int age;
	private Location location;

	// getters and setters...
}
```

**_Location.java_**

``` Java
public class Location {
	private String city;
	private String state;

	//getters and setters...
}
```
The descriptor is a JSON file... more info are coming!

##Content aggregation

Suppose you have 2 different resources, one to get a city and the other one to get city's state/country (I know, but it's just an example), and you want to have all information as one result (city's name and city's country name), what you can do is to define/deploy a new resource which interacts with both resources to aggregate/mix the content and give you the result you want.

I'll show you.

**_city.json_**

``` JSON
{"name":"city",
"value":
        {"responder":"com.foogaro.resty.adapter.responder.MySQL",
        "query":"SELECT * FROM TBL_CITY",
	"input": {"NAME":"Rome"}
        "return":"com.foogaro.resty.test.data.City",
        "content-type":["application/xml","application/json"]
        }

}
```

**_country.json_**

``` JSON
{"name":"country",
"value":
        {"responder":"com.foogaro.resty.adapter.responder.MySQL",
        "query":"SELECT * FROM TBL_COUNTRY",
        "input": {"CITY_NAME":"Rome"}
        "return":"com.foogaro.resty.test.data.Country",
        "content-type":["application/xml","application/json"]
        }

}
```

**_mixing-city-country.json_**

``` JSON
{"name":"cityCountry",
"value":
	{"responder":"com.foogaro.resty.adapter.responder.Resource",
	"input":[
		{"resource":"city","input":{"NAME":"Rome"},
		"resource":"country","input":{"CITY_NAME":"Rome"}
		]},
	"return":{"resources": [
			{"resource":"city","get":"name"},
			{"resource":"country","get":"name"}],
		"separator":" - "
		}
	}
}

```

The result would be:
"Rome - Italy"

All you had to do was deploy a ROAR with the *mixing-city-country.json* resource file.
