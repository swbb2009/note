# 普日docker下导出oracle

docker exec -it docker_oracle-ee-12c_1 expdp yshj1126/123456@localhost/pdb1 directory=DUMP_DIR dumpfile=product124_0422.dmp


docker exec -it xtv2_pep1_xt-oracle-ee-12c_1 impdp yshj0422/123456@localhost/pdb1 directory=DUMP_DIR dumpfile=product124_0422.dmp   table_exists_action=replace full=y  remap_schema=yshj0716:yshj0422