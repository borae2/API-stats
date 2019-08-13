[2019-08-13T11:48:50,648][INFO ][o.e.c.c.Coordinator      ] [adp-ela-4] cluster UUID [5AXFfTbCTVKQe2ZNuFqE5Q]
[2019-08-13T11:49:00,666][WARN ][o.e.c.c.ClusterFormationFailureHelper] [adp-ela-4] master not discovered or elected yet, an election requires a node with id [mWAy_-00T2W1n9o5HRZW9g], have discovered [] which is not a quorum; discovery will continue using [127.0.0.1:9300, 127.0.0.1:9301, 127.0.0.1:9302, 127.0.0.1:9303, 127.0.0.1:9304] from hosts providers and [{adp-ela-4}{0LhUpSDURUutDw34DRW8og}{1IE1-0GXS26yD1RjR5fxuQ}{10.113.108.42}{10.113.108.42:9300}{ml.machine_memory=8356007936, xpack.installed=true, ml.max_open_jobs=20}] from last-known cluster state; node term 1, last-accepted version 40 in term 1
[2019-08-13T11:49:10,668][WARN ][o.e.c.c.ClusterFormationFailureHelper] [adp-ela-4] master not discovered or elected yet, an election requires a node with id [mWAy_-00T2W1n9o5HRZW9g], have discovered [] which is not a quorum; discovery will continue using [127.0.0.1:9300, 127.0.0.1:9301, 127.0.0.1:9302, 127.0.0.1:9303, 127.0.0.1:9304] from hosts providers and [{adp-ela-4}{0LhUpSDURUutDw34DRW8og}{1IE1-0GXS26yD1RjR5fxuQ}{10.113.108.42}{10.113.108.42:9300}{ml.machine_memory=8356007936, xpack.installed=true, ml.max_open_jobs=20}] from last-known cluster state; node term 1, last-accepted version 40 in term 1


* unicasts_hosts.txt에 마스터를 찾는게 없으면, 선출하지 못함 (elastic.yml에 자기자신도 있다면 찾을듯

* 다음 테스트네용 : 
1] 따른애 못찾고, 자기자신도 마스터노드 포함시켜봄
2] 따른애만 추가해놓고 (4번을 1번대에추가)
   1번을 죽여봄
   
