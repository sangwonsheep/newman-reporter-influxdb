# newman-reporter-influxdb

https://github.com/vs4vijay/newman-reporter-influxdb 

해당 라이브러리에서 `InfluxDB 2.x 버전의 token 인증 방식을 지원하지 않습니다.`   
token 인증 방식 지원을 위해 직접 코드를 수정하여 `InfluxDB 2.x 버전의 token 방식을 추가`하였습니다.   
`Newman`의 결과를 `InfluxDB`에 저장 후 `Grafana를` 이용해 시각화하기 위해 사용합니다.

## Getting Started

1. Install `newman`
2. Install `newman-reporter-influxdb`
3. Install `InfluxDB` (Get the server address, port, database name, etc)

## Usage

Specify `-r influxdb` option while running the collection

```bash
newman run <collection-url> -r influxdb \
  --reporter-influxdb-server <server-ip> \
  --reporter-influxdb-port <server-port> \
  --reporter-influxdb-name <database-name> \
  --reporter-influxdb-measurement <measurement-name>
```

- By default, reporter consider influxdb version 1.x (i.e 1.7, 1.8)
- In case of InfluxDB version 2, specify version, org and bucket name as well
  - `--reporter-influxdb-version 2`
  - `--reporter-influxdb-org <org-name>`
  - `--reporter-influxdb-name <bucket-name>`

Example:

```
# For InfluxDB version 2.x (아래 코드는 2.7 기준)

# Node 설치
- name: Set up Node
  uses: actions/setup-node@v4
  with:
    node-version: 20

# Newman, InfluxDB reporter 설치
- name: Install newman and InfluxDB reporter
  run: |
    npm install -g newman
    npm install -g git+https://github.com/sangwonsheep/newman-reporter-influxdb.git # npm 배포한 것이 아니므로 이렇게 사용

# Newman API 테스트 실행 결과 InfluxDB에 적재
- name: Run Postman collection
  run: |
    newman run ${{ secrets.POSTMAN_COLLECTION_URL }} \
      -r influxdb \
      --reporter-influxdb-org ${{ secrets.INFLUXDB_ORG }} \
      --reporter-influxdb-name ${{ secrets.INFLUXDB_BUCKET }} \
      --reporter-influxdb-username ${{ secrets.INFLUXDB_USERNAME }} \
      --reporter-influxdb-password ${{ secrets.INFLUXDB_PASSWORD }} \
      --reporter-influxdb-token ${{ secrets.INFLUXDB_TOKEN }} \
      --reporter-influxdb-version 2 \
      --reporter-influxdb-measurement newman_metrics \
      --reporter-influxdb-server influxdb.example.com \ # InfluxDB를 띄운 url 사용 (요청 보낼 경로)
      --reporter-influxdb-mode https \
      --reporter-influxdb-port 443 # https 포트 (InfluxDB port X)
```

### Options:

**Option** | **Remarks**
--- | --- 
`--reporter-influxdb-server` | IP Address or Host of InfluxDB
`--reporter-influxdb-port` | Port no. (Usually `8086`)
`--reporter-influxdb-version` | InfluxDB Version `1`, `2` (default `1`)
`--reporter-influxdb-org` | InfluxDB Org (For InfluxDB version 2.x)
`--reporter-influxdb-token` | InfluxDB API Token (For InfluxDB version 2.x)
`--reporter-influxdb-name` | Database name (or Bucket name for InfluxDB version 2.x)
`--reporter-influxdb-measurement` | Measurement Point name (If not provided, then reporter will create measurement with prefix `newman_results-<timestamp>`)
`--reporter-influxdb-username` (*Optional*) | Username created for InfluxDB (e.g. `newman_user`)
`--reporter-influxdb-password` (*Optional*) | Password of the user (e.g. `p@ssw0rd`)
`--reporter-influxdb-identifier` (*Optional*) | An identifier to be passed to InfluxDB (default: `run-${Date.now()}`)
`--reporter-influxdb-mode` | Transmission Mode `http`, `https`, `udp` (default: `http`)
`--reporter-debug` | Enable debug mode (default: `false`)

---
