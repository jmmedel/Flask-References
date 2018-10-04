

Flask – Sijax
Advertisements
 Previous Page Next Page  
Sijax stands for ‘Simple Ajax’ and it is a Python/jQuery library designed to help you easily bring Ajax to your application. It uses jQuery.ajax to make AJAX requests.

Installation
Installation of Flask-Sijax is easy.

pip install flask-sijax
Configuration
SIJAX_STATIC_PATH − the static path where you want the Sijax javascript files to be mirrored. The default location is static/js/sijax. In this folder, sijax.js and json2.js files are kept.

SIJAX_JSON_URI − the URI to load the json2.js static file from

Sijax uses JSON to pass the data between the browser and the server. This means that the browsers need either to support JSON natively or get JSON support from the json2.js file.

Functions registered that way cannot provide Sijax functionality, because they cannot be accessed using a POST method by default (and Sijax uses POST requests).

To make a View function capable of handling Sijax requests, make it accessible via POST using @app.route('/url', methods = ['GET', 'POST']) or use the @flask_sijax.route helper decorator like this −

@flask_sijax.route(app, '/hello')
Every Sijax handler function (like this one) receives at least one parameter automatically, much like Python passes ‘self’ to the object methods. The ‘obj_response’ parameter is the function's way of talking back to the browser.

def say_hi(obj_response):
   obj_response.alert('Hi there!')
When Sijax request is detected, Sijax handles it like this −

g.sijax.register_callback('say_hi', say_hi)
   return g.sijax.process_request()
Sijax Application
A minimal Sijax application code looks as follows −

import os
from flask import Flask, g
from flask_sijax import sijax

path = os.path.join('.', os.path.dirname(__file__), 'static/js/sijax/')
app = Flask(__name__)

app.config['SIJAX_STATIC_PATH'] = path
app.config['SIJAX_JSON_URI'] = '/static/js/sijax/json2.js'
flask_sijax.Sijax(app)

@app.route('/')
def index():
   return 'Index'
	
@flask_sijax.route(app, '/hello')
def hello():
   def say_hi(obj_response):
      obj_response.alert('Hi there!')
   if g.sijax.is_sijax_request:
      # Sijax request detected - let Sijax handle it
      g.sijax.register_callback('say_hi', say_hi)
      return g.sijax.process_request()
      return _render_template('sijaxexample.html')

if __name__ == '__main__':
   app.run(debug = True)
When a Sijax requests (a special jQuery.ajax() request) to the server, this request is detected on the server by g.sijax.is_sijax_request(), in which case you let Sijax handle the request.

All the functions registered using g.sijax.register_callback() are exposed for calling from the browser.

Calling g.sijax.process_request() tells Sijax to execute the appropriate (previously registered) function and return the response to the browser.
