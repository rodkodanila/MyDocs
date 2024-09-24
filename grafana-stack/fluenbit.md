# 1. Install fluentbit
```
apt install gpg -y
curl https://raw.githubusercontent.com/fluent/fluent-bit/master/install.sh | sh
```
# 2. Start/Enable service
sudo systemctl start fluent-bit
sudo systemctl enable fluent-bit
sudo systemctl status fluent-bit

# 3. Change config if you need
```
cat << EOF > /etc/fluent-bit/fluent-bit.conf
[SERVICE]
    flush        1
    daemon       Off
    log_level    info
    Parsers_File parsers.conf

[INPUT]
    Name tcp
    Listen 0.0.0.0
    Port 4444
    Format json
    Tag tcp_json_logs


[FILTER]
    Name parser
    Match tcp_json_logs
    Key_Name log
    Parser json_parser

[FILTER]
    Name modify
    Match tcp_json_logs
    Rename log message


[OUTPUT]
    Name stdout
    Match *

[OUTPUT]
    name                   loki
    match                  *
    host                   127.0.0.1
    port                   80
    http_user              test
    http_passwd            test
    labels job=fluentbit-pud,username=$userName,method=$method,requestURI=$requestURI,responseCode=$responseCode,address=$address
#    tenant_id             test1
EOF
```
```
cat << EOF > test.json
{
"id": null,
"method": "POST",
"requestURI": "/api/securities_service/securities",
"requestPayload": "{\"finBaseRateName\":\"Ставка рефинансирования Нациоанального банка Республики Беларусь\",\"numSecurities\":\"14\",\"frequencyComment\":null,\"numDocVolume\":null,\"currencyShortName\":\"BYN\",\"flagStrip\":false,\"idResidenceTypeAddit\":null,\"residenceTypeMain\":\"Резиденты\",\"securitiesResolutionParent\":\"Указ Президента Республики Беларусь 147 от 05.05.2017\",\"dateSecurityPlacementFrom\":\"29.05.2024\",\"idResidenceTypeMain\":1,\"lotsQuantity\":null,\"idPaymentPeriod\":2,\"dateCancel\":null,\"percentPeriodComment\":null,\"state\":\"REAL\",\"nameGarant\":\"Могилевский областной исполнительный комитет\",\"flagDebtInternal\":true,\"quantityFirstLot\":null,\"volume\":\"3541000.00\",\"idFinBaseRates\":34,\"daysToMaturity\":905,\"repaymentVolume\":\"3541000.00\",\"idSecuritiesPlaceMethods\":3,\"shortName\":\"ДО (П)\",\"idSecuritiesIssuePurposes\":142,\"dateIssue\":\"29.05.2024\",\"dateSecurityPlacementTo\":\"09.01.2027\",\"idDocTypeParent\":3,\"flagExistsSecuritiesHoldersTypesAddit\":false,\"income\":\"PERCENT_VARIABLE\",\"quantityRepaidSecurities\":35410,\"bondsSellType\":\"CLOSED\",\"placementPeriod\":3560,\"securitiesResolution\":\"№ 21-3 от 24.05.2017\",\"purpose\":\"Финансовое оздоровление и повышение эффективности деятельности организаций АПК Могилевской области (Для приобретения активов по кредитам от Беларусбанка (Агромашсервис)\",\"idSecuritiesTypes\":11,\"idSecuritiesResolutions\":201,\"idUserRegion\":70100,\"flagLot\":false,\"flagIssueSuccessful\":true,\"dateEnd\":\"26.02.2027\",\"idCurrency\":933,\"sellType\":\"Закрытая продажа Прямая продажа юридическим лицам по договору\",\"nominal\":\"100.00\",\"currencyName\":\"Белорусский рубль\",\"namePeriod\":\"Месяц\",\"flagOpenSale\":false,\"idsSecuritiesHoldersTypesMain\":[2],\"quantityPeriods\":30,\"numRegistration\":\"7-401-02-2998\",\"idsSecuritiesHoldersTypesAddit\":null,\"quantity\":35410,\"dateDocVolume\":null,\"cfi\":null,\"amountPeriods\":1,\"fisn\":null,\"holderTypeMain\":\"Юридическое лицо\",\"debtString\":\"Внутренний\",\"periodicity\":\"Месяц 1\",\"comment\":null,\"periodPlacement\":\"с 29.05.2017 по 09.06.2017\",\"codeSecurities\":\"BY74012B7264\",\"isin\":null,\"currentValue\":null}",
"responseCode": 422,
"responsePayload": "\"{\\\"timestamp\\\":\\\"2024-09-06T12:02:27.8634022\\\",\\\"message\\\":\\\"Ошибка валидации\\\",\\\"status\\\":\\\"UNPROCESSABLE_ENTITY\\\",\\\"path\\\":\\\"uri=/api/securities_service/securities\\\",\\\"errors\\\":{\\\"dateSecurityPlacementFrom\\\":\\\"Поле 'Дата начала размещения' На выбраную дату отсутсвует ставка\\\"}}\"",
"timeRequest": 40,
"userName": "test",
"address": "0:0:0:0:0:0:0:1",
"message": null,
"dateRequest": "2024-09-06T12:02:27.8644237",
"errorMessage": null,
"errorStackTrace": null
}
EOF
```
## insert into fluentbit
```
/opt/fluent-bit/bin/fluent-bit -c /etc/fluent-bit/fluent-bit.conf
cat test.json | nc 127.0.0.1 4444
```
