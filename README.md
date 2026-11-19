# anclas-cadena-senales

Anclas diarias de una cadena de hashes (SHA-256) de señales publicadas.

Cada día hábil, después del cierre, se agrega `anchors/AAAA-MM-DD.json` con:

- `anchor_date`: la fecha;
- `last_seq`: el número de la última fila de la cadena ese día;
- `last_chain_hash`: el hash de esa fila;
- `genesis` y `genesis_hash`: el comienzo de la cadena (el `prev_hash` de la primera fila es el SHA-256 del génesis);
- `spec_version` y `algoritmo`: cómo se calcula cada hash.

Junto a cada ancla va su sello de [OpenTimestamps](https://opentimestamps.org) (`anchors/AAAA-MM-DD.json.ots`), que la ata a un bloque de Bitcoin y prueba que el archivo existía en esa fecha.

## Cómo verificar

1. Recorrer las filas publicadas en orden de `seq`. Para cada una:
   `chain_hash = sha256(prev_hash || JSON canónico de la fila sin prev_hash ni chain_hash)` (claves ordenadas, sin espacios, UTF-8).
   El `prev_hash` de la primera fila es `sha256(genesis)`; el de cada una de las demás, el `chain_hash` de la anterior.
2. La fila `last_seq` del ancla tiene que tener exactamente ese `last_chain_hash`. Si coincide, ninguna fila anterior a esa fecha se insertó, editó ni borró después.
3. El sello: `ots verify anchors/AAAA-MM-DD.json.ots`.

Este repositorio no contiene señales ni datos de operación: sólo las anclas.
