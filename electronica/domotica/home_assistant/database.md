https://www.home-assistant.io/docs/backend/database/

Por defecto sqlite, fichero home-assistant_v2.db

Ejemplos de código y queries borrando datos:
https://community.home-assistant.io/t/modifying-data-in-the-database-via-script/36103/5

El histórico se almacena en la tabla "states"

Borrar histórico, ejemplo:
delete from states where entity_id = "sensor.ble_non_stabilized_weight_volumen_deposito_agua" ;
