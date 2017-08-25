## request

* 获取get的参数

  ```python
  from flask import request
  ...
  @app.route('/xxx', methods=['GET'])
  def xxx():
  	...
  	a = request.args.get('xxx')
  ```

* 获取post参数

  ```python
  from flask import request
  ...

  @app.route('/xxx', methods=['POST'])
  def xxx():
    ...
    r = request.form.get('xxx')
  ```

* 获取json字符串(对应的是("Content-type:application/json;charset=utf-8"))

  ```python
  from flask import request

  @app.route('/xxx', methods=['GET', 'POST'])
  def xxx():
    ...
    r = request.get_json()
  ```

  ​

