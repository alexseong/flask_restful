# Flask RESTful

<b>Flask-RESTful</b> is an extension for Flask that adds support for quickly building REST APIs. It is a lightweight abstraction that works with your existing ORM/libraries. Flask-RESTful encourages best practices with minimal setup. If you are familiar with Flask, Flask-RESTful should be easy to pick up.<br>

## User's Guide

### Installation

Install Flask-RESTful with pip
~~~
pip install flask-restful
~~~
The development version can be downloaded from its page at GitHub.

~~~
git clone https://github.com/flask-restful/flask-restful.git
cd flask-restful
python setup.py develop
~~~
Flask-RESTful has the following dependencies (which will be automatically installed if you use pip):

* Flask version 0.10 or greater

Flask-RESTful requires Python version 2.7, 3.4, 3.5, 3.6 or 3.7

### A Minimal API

A minimal Flask-RESTful API looks like this:

~~~
from flask import Flask
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)

class HelloWorld(Resource):
    def get(self):
        return {'hello': 'world'}

api.add_resource(HelloWorld, '/')

if __name__ == '__main__':
    app.run(debug=True)
~~~

~~~
$ python api.py
 * Running on http://127.0.0.1:5000/
 * Restarting with reloader
~~~

Now open up a new prompt to test out your API using curl

~~~
$ curl http://127.0.0.1:5000/
{"hello": "world"}
~~~

###Resourceful Routing

The main building block provided by Flask-RESTful are resources. Resources are built on top of Flask pluggable views, giving you easy access to multiple HTTP methods just by defining methods on your resource. A basic CRUD resource for a todo application (of course) looks like this:

~~~
from flask import Flask, request
from flask_restful import Resource, Api

app = Flask(__name__)
api = Api(app)

todos = {}

class TodoSimple(Resource):
    def get(self, todo_id):
        return {todo_id: todos[todo_id]}

    def put(self, todo_id):
        todos[todo_id] = request.form['data']
        return {todo_id: todos[todo_id]}

api.add_resource(TodoSimple, '/<string:todo_id>')

if __name__ == '__main__':
    app.run(debug=True)
~~~
You can try it like this:
~~~
$ curl http://localhost:5000/todo1 -d "data=Remember the milk" -X PUT
{"todo1": "Remember the milk"}
$ curl http://localhost:5000/todo1
{"todo1": "Remember the milk"}
$ curl http://localhost:5000/todo2 -d "data=Change my brakepads" -X PUT
{"todo2": "Change my brakepads"}
$ curl http://localhost:5000/todo2
{"todo2": "Change my brakepads"}
~~~
Or from python if you have the requests library installed:
~~~
>>> from requests import put, get
>>> put('http://localhost:5000/todo1', data={'data': 'Remember the milk'}).json()
{u'todo1': u'Remember the milk'}
>>> get('http://localhost:5000/todo1').json()
{u'todo1': u'Remember the milk'}
>>> put('http://localhost:5000/todo2', data={'data': 'Change my brakepads'}).json()
{u'todo2': u'Change my brakepads'}
>>> get('http://localhost:5000/todo2').json()
{u'todo2': u'Change my brakepads'}
~~~
Flask-RESTful understands multiple kinds of return values from view methods. Similar to Flask, you can return any iterable and it will be converted into a response, including raw Flask response objects. Flask-RESTful also support setting the response code and response headers using multiple return values, as shown below:

~~~
class Todo1(Resource):
    def get(self):
        # Default to 200 OK
        return {'task': 'Hello world'}

class Todo2(Resource):
    def get(self):
        # Set the response code to 201
        return {'task': 'Hello world'}, 201

class Todo3(Resource):
    def get(self):
        # Set the response code to 201 and return custom headers
        return {'task': 'Hello world'}, 201, {'Etag': 'some-opaque-string'}
~~~