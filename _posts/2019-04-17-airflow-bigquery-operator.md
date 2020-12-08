---
layout: post
title:  "Airflow BigQuery Operator의 이해"
subtitle: "Airflow BigQuery Operator"
categories: mlops
tags: airflow
comments: true
---

- Apache Airflow의 BigQuery Operator에 대한 글입니다

---

### BigQuery
- BigQuery는 Google Cloud Platform에서 매우 좋은 평가를 받고 있는 Managed 데이터 웨어하우스
- 데이터 분석용 데이터베이스로 매우 좋고, 빠른 속도가 장점
- 더 자세한 내용은 [공식 문서](https://cloud.google.com/bigquery/) 또는 [Google BigQuery Users 참고 자료](https://www.facebook.com/groups/bigquery/permalink/1760585197568366/) 참고!


### Airflow Operator
- Apache Airflow는 AWS/GCP Operator들이 잘 구현되어 있음
	- 굳이 따지면 GCP 쪽 Operator가 더 잘되어 있는 편
	- [공식 문서](http://airflow.apache.org/integration.html?highlight=bigquery#gcp) 
- BigQuery Operator는 내부적으로 [BigQueryHook](http://airflow.apache.org/_api/airflow/contrib/hooks/bigquery_hook/index.html#airflow.contrib.hooks.bigquery_hook.BigQueryHook)을 사용해 Google Cloud Platform과 연결
	- 처음 Operator 사용할 땐 "내부적으로 Hook을 통하는구나" 정도로 먼저 이해한 후, 사용해도 무방

### BigQuery Operator
- [공식 문서](http://airflow.apache.org/integration.html?highlight=bigquery#bigquery)
- [Github Link](https://github.com/apache/airflow/tree/master/airflow/contrib/operators)
- 총 11개 Operator가 존재(1.10.1 버전 기준)
	- BigQueryCheckOperator
	- BigQueryIntervalCheckOperator
	- BigQueryValueCheckOperator
	- BigQueryGetDataOperator
	- BigQueryCreateEmptyDatasetOperator
	- BigQueryCreateExternalTableOperator
	- BigQueryDeleteDatasetOperator
	- BigQueryOperator
	- BigQueryTableDeleteOperator
	- BigQueryToBigQueryOperator
	- BigQueryToCloudStorageOperator
- 사용하는 사람마다 다르겠지만 제가 제일 많이 사용하는 Operator는 `BigQueryOperator`, `BigQueryCreateExternalTableOperator`, `BigQueryTableDeleteOperator`, `BigQueryToBigQueryOperator`
- BigQueryOperator
	- [공식 문서](http://airflow.apache.org/_api/airflow/contrib/operators/bigquery_operator/index.html#airflow.contrib.operators.bigquery_operator.BigQueryOperator)
	- BigQuery SQl 쿼리를 날려주는 Operator
	- 쿼리 결과를 Table로 저장할 수 있음
	- 사용 예시
	 
	```
	from airflow import models
	from airflow.contrib.operators.bigquery_operator import BigQueryOperator
	
	default_dag_args = {
	    'owner': 'zzsza',
	    'start_date': datetime(2019, 4, 2),
	    'email': ['your_email@gmail.com'],
	    'email_on_failure': False,
	    'email_on_retry': False,
	    'retries': 0,
	    'project_id': 'your_proeject_name'
	}

	query = """
		SELECT *
		FROM `project.dataset.table`
		LIMIT 1000
	"""
	
	with models.DAG(
        dag_id = 'extract_feature_dag',
        schedule_interval= '30 0 * * *',
        default_args=default_dag_args) as dag:

	    bq_query = BigQueryOperator(
	        task_id='extract_daily_metric',
	        bql=query, 
	        use_legacy_sql=False,
	        destination_dataset_table='dataset.table',
	        write_disposition='WRITE_TRUNCATE'
	        )
	
	    bq_query
	```
	
	- 파라미터 (몇개만 설명)
	
	```
	BigQueryOperator(bql=None, sql=None, destination_dataset_table=None, write_disposition='WRITE_EMPTY', \
		allow_large_results=False, flatten_results=None, bigquery_conn_id='bigquery_default', delegate_to=None, \
		udf_config=None, use_legacy_sql=True, maximum_billing_tier=None, maximum_bytes_billed=None, \
		create_disposition='CREATE_IF_NEEDED', schema_update_options=(), query_params=None, labels=None, \
		priority='INTERACTIVE', time_partitioning=None, api_resource_configs=None, cluster_fields=None, \
		location=None, *args, **kwargs)
	```
		
	- bql는 이제 deprecate 예정, sql 사용(.sql 파일도 사용 가능)
	- destination\_dataset\_table : `<project>.<dataset>.<table>` 형태로 사용
	- write_disposition : WRITE\_EMPTY(빈 경우만 쓰기), WRITE\_TRUNCATE(덮어 쓰기), WRITE\_APPEND(데이터 Append), [참고 문서](https://cloud.google.com/bigquery/docs/reference/rest/v2/jobs)
	- bigquery\_conn\_id : Connection 설정 이름
- BigQueryCreateExternalTable
	- GoogleCloudStorageToBigQueryOperator과 유사한 작업을 하는 Operator로 BigQueryCreateExternalTable은 Bigtable, Google Storage, Google Drive 등에서 데이터를 가지고 올 수 있음(더 넓은 개념)

	```
	date = "{#{ macros.ds_format(macros.ds_add(ds, -1), '%Y-%m-%d', '%Y/%m/%d') }#}" # 코드에선 # 빼주세요
	
	create_table = BigQueryCreateExternalTable(bucket='bucket_name', source_objects=[f'{date}/user_log.csv'],
										schema_fields =[{"name": "user_id", "type": "STRING", "mode": "REQUIRED"}, {"name":"purchase_amount", "type": "INTEGER", "mode": "NULLABLE"}],
										schema_object='bigquery-schema/user_log.json' # Google Storage path,
										source_format='CSV',
										bigquery_conn_id='bigquery_default',
										google_cloud_storage_conn_id='storage_default'
	```
	
- BigQueryDeleteDatasetOperator
	- 데이터셋 삭제 Operator
	
	```
	delete_temp_data = BigQueryDeleteDatasetOperator(dataset_id = 'temp-dataset',
                                         project_id = 'temp-project',
                                         bigquery_conn_id='_my_gcp_conn_',
                                         task_id='Deletetemp',
                                         dag=dag)
 ```	 
 
- BigQueryToBigQueryOperator
	- BigQuery Table A를 B로 옮길 때 사용하는 Operator
	- 데이터 마이그레이션시 사용
	
	```
	move_table = BigQueryToBigQueryOperator(source_project_dataset_tables='project.dataset.table',  \
									destination_project_dataset_talbe='project.dataset.tableb', write_disposition='WRITE_TRUNCATE', \
									bigquery_conn_id='bigquery_default')
	```  
	
### 참고 자료
- [Apache Airflow - Workflow 관리 도구(1)](https://zzsza.github.io/data/2018/01/04/airflow-1/)	
