

pycharm 代码提交前的配置**



1. 安装

   1. pip install pre-commit
   2. pip install isort
   3. pip install black
   4. pip install flake8

2. 在该项目根目录运行pre -commit install

3. 在根目录新建.pre-commit-config配置文件

4. 配置内容如下   提交前按顺序执行3个插件

   ~~~ yaml
   repos:
   - repo: https://github.com/pycqa/isort
     rev: 5.12.0  # isort版本号（可选）
     hooks:
       - id: isort
   - repo: https://github.com/ambv/black
     rev: stable
     hooks:
       - id: black
         language_version: python3.9
   - repo: https://github.com/pre-commit/pre-commit-hooks
     rev: v1.2.3
     hooks:
      - id: flake8
   ~~~
