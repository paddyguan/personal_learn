## hashlib

* md5

  ```python
  import hashlib

  md5 = hashlib.md5()
  md5.update(target_string)
  result_string = md5.hexdigest()
  # 对字符串进行大写或全小写操作
  result_string = result_string.upper()
  result_string = result_string.lower()
  ```

  ​

  ​

