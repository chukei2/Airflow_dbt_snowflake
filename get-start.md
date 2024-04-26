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

