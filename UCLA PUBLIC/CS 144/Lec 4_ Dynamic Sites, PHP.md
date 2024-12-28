- Web 1.0: static sites
- Web 2.0: Dynamic and interactive
- Web 3.0: Semantic web
	- Web3 is www built on blockchain
## Forms
```html
<form action="https://www.google.com/search" method="GET">
	<input name="q" type="text">
	<input type="submit">
</form>
```
- Native: button, checkbox, file, hidden (maintain state), image, password, radio, reset, submit
- HTML5: email, search, tel, url, numeric, slider/range, date/time picker, color picker
- Method get: URL params
- Method POST: set using application/x-www-form-urlencoded
	- Must set `enctype` to multipart/form-data for file input type
## PHP
```php
<?php
	$name = $_GET['name'];
	echo("<html><head></head><body>");
	echo("<p>Hello, $name</p>");
	echo("</body></html>");
?>
<!-- OR -->
<p>Hello, <?php $_GET['name']?></p>
```
Example template (Jinja/PHP Twig)
```html
<body>
	My name is {{ name }}.
	My friends are:
	<ul>
	{% for person in friends %}
		<li>{{ person.firstname }} {{ person.lastname }}</li>
	{% endfor %}
	</ul>
</body>
```
## MVC
- Model-view-controller
	- Model = data (data engineer or DBA)
	- controller = application level logic (app dev)
	- view = final presentation (UI designer)

Python: Flask, Django, FastAPI
Ruby: “Ruby on Rails”
Java: Struts, Spring, Grails
JavaScript: Angular, React Redux etc., Vue.js
PHP: Laravel, CodeIgniter, Yii, Zend
Perl: Catalyst
```python
from flask import Blueprint
from controllers.machineController import index, create, insert

blueprint = Blueprint('blueprint', __name__)

blueprint.route('/', methods=['GET'])(index)
blueprint.route('/create', methods=['GET'])(create)
blueprint.route('/insert', methods=['GET'])(insert)

from flask_sqlalchemy import SQLAlchemy
db = SQLAlchemy()
# Creating the Inserttable for inserting data into the database
class Inserttable(db.Model):
	'''Data for ON/OFF should be dumped in this table.'''
	__tablename__ = 'inserttable'
	id = db.Column(db.Integer, primary_key=True, autoincrement=True)
	machineid = db.Column(db.Integer, primary_key=False)
	stateid = db.Column(db.Integer, primary_key=False)
	...

	# method used to represent a class's objects as a string
	def __repr__(self):
		return '<machineid %r>' % self.machineid
	
import json
from models.machine import Inserttable, db
from services.user_service import insert_logic, create_logic
def index():
	return {'status': 'OK',
	'localhost:5000/machines/create': 'Create table in mysql',
	'localhost:5000/machines/insert': 'Insert data in table(Inserttable)'}

def create():
	create_logic()

# insert data into table.
def insert():
	insert_logic()
```
## AJAX
- Async Javascript and XML (JSON)