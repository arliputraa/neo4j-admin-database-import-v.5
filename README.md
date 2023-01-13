# Neo4j 5 admin import database
Initial load and incremental load (used to incrementally import data into an existing database)

## Step 1
### Initial Load

    ./neo4j-admin database import full --auto-skip-subsequent-headers=true --delimiter="," --skip-duplicate-nodes=true --overwrite-destination=true --skip-bad-relationships=true \
    --nodes=Customer=/home/ddi/header/head_df_cust.csv,/home/ddi/data/batch32/df_cust.csv \
    --nodes=Company=/home/ddi/header/head_df_comp.csv,/home/ddi/data/batch32/df_comp.csv \
    --relationships=MEMBER_OF=/home/ddi/header/rel/head_rel_cust_comp.csv,/home/ddi/data/batch32/df_cust_comp.csv \
    databasename
    
## Step 2
### Create Constraint

    CREATE CONSTRAINT Customer IF NOT EXISTS
    FOR (c:Customer) REQUIRE c.customer_id IS UNIQUE;

    CREATE CONSTRAINT Company IF NOT EXISTS
    FOR (c:Company) REQUIRE c.company_id IS UNIQUE;

## Step 3
### Stop Database in System

    stop database databasename WAIT;
    
## Step 4

create header node incremental

    customer_id:ID(CUST-ID){label:Customer},phone,province
    company_id:ID(COMP-ID){label:Company},segmen,sector

### Incremental Load
    ./neo4j-admin database import incremental --stage=all --auto-skip-subsequent-headers=true --delimiter=","  --skip-duplicate-nodes=true --force \
    --nodes=Customer=/home/ddi/header2/head_df_cust.csv,/home/ddi/data/batch34/df_cust.csv \
    --nodes=Product=/home/ddi/header2/head_df_product.csv,/home/ddi/data/batch34/df_product.csv \
    --relationships=HAS_TRANSACTION=/home/ddi/header/rel/head_rel_cust_product.csv,/home/ddi/data/batch34/trx.csv \
    databasename

## Step 5
    start database databasename WAIT;
