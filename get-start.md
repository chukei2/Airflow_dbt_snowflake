# 環境設定
## ターミナルで次のコマンドを実行してください:

```
curl -LfO 'https://airflow.apache.org/docs/apache-airflow/2.3.0/docker-compose.yaml'
```

## 追加されたdocker-compose.yamlのvolumesを次のように置き換えてください:

```
  volumes:
    - ./dags:/opt/airflow/dags
    - ./logs:/opt/airflow/logs
    - ./plugins:/opt/airflow/plugins
    - ./dbt:/dbt # add this in
    - ./dags:/dags # add this in
```

## `.env`ファルを作成し次の環境変数を追加してください:

```
_PIP_ADDITIONAL_REQUIREMENTS=dbt==0.19.0 
```

## `dbt init dbt`を実行してください
### dbtのインストール方法(dbt-snoflake編)
```
$ brew update
$ brew install git  
$ brew tap dbt-labs/dbt
$ brew install dbt-snowflake
```


#  DBTプロジェクトの設定
Snowflake ワークシートで次のコマンドを実行してください:
```
use role SYSADMIN;
use database <YOUR_DB>;
create schema <FREE_NAME>;
```

実行後、schemaが作られていることが確認できます。

## `macros`フォルダを作成し、`custom_demo_macros.sql`ファイルを作成する。
`custom_demo_macros.sql`に次の内容を加えてください:
```
{% macro generate_schema_name(custom_schema_name, node) -%}
    {%- set default_schema = target.schema -%}
    {%- if custom_schema_name is none -%}
        {{ default_schema }}
    {%- else -%}
        {{ custom_schema_name | trim }}
    {%- endif -%}
{%- endmacro %}


{% macro set_query_tag() -%}
  {% set new_query_tag = model.name %} {# always use model name #}
  {% if new_query_tag %}
    {% set original_query_tag = get_current_query_tag() %}
    {{ log("Setting query_tag to '" ~ new_query_tag ~ "'. Will reset to '" ~ original_query_tag ~ "' after materialization.") }}
    {% do run_query("alter session set query_tag = '{}'".format(new_query_tag)) %}
    {{ return(original_query_tag)}}
  {% endif %}
  {{ return(none)}}
{% endmacro %}
```
