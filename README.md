RESTY
=====

REST Server

What is it? Or what is not?
---------------------------

Resty is a HTTP Server which is not a NGNiX or Apache HTTP clone, and is not even a Tomcat HTTP Servlet Container.
Resty is just new approach for serving resources.
Flexible, extensible, just new.
It's an idea, maybe a bad idea... who konws.
It's written in Java... I might switch to Scala.


How it works
------------

You deploy ROAR files, which are Rest Oriented ARchive.
These files are unpacked as normal WAR or EAR in memory, not the HEAP though.

Inside the roar you write as many descriptor as you like, every descriptor file is intended to describe your resource.
Each descriptor can describe one or more resource. Each resource is described inside the descriptor file or in a different file, used just for the resource.

Here is an example of a descriptor file:


test.json
---------
//
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



resource.json
-------------

{"name":"cities",
"value":
	{"responder":"com.foogaro.resty.adapter.responder.MySQL",
	"query":"SELECT * FROM TBL_USERS"
	"mapping":{"COLUMN_AGE":"age","COLUMN_SEX":"gender","COLUMN_LOCATION":"location"}
	"return":"com.foogaro.resty.test.data.Person",
	"content-type":["application/xml","application/json"]
	}
	
}


Person.java
-----------

public class Person {
	private String name;
	private String gender;
	private int age;
	private Location location;

	/ getters and setters...
}


Location.java
-------------

public class Location {
	private String city;
	private String state;

	//getters and setters...
}

The descriptor is a JSON file... more info are coming!

