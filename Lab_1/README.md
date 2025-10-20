## 1) TABLA HEAP (sin orden, sin índices)
CREATE TABLE estudiantes_heap AS
SELECT * FROM estudiantes;
 
Esa tabla se crea sin ORDER BY ni índices → almacenamiento tipo HEAP.

# La verificación sin índices:

SELECT * FROM pg_indexes WHERE tablename = 'estudiantes_heap';

## 2) TABLA ORDENADA / USO DE ÍNDICE
CREATE TABLE estudiantes_ordenados AS
SELECT * FROM estudiantes ORDER BY id_estudiante;


-- Aquí los datos se guardan ordenados físicamente.

# Luego se crea índice (para simular orden físico):

CREATE INDEX idx_estudiantes_ordenados_id
ON estudiantes_ordenados(id_estudiante);


# Las consultas de prueba:

EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM estudiantes_ordenados WHERE id_estudiante = 1077;


Ahí puedes ver si usa Index Scan o no.

## 3) HASH (distribución y particiones)

Hash manual (para mostrar colisiones):

SELECT
	id_estudiante,
	hash_estudiante(id_estudiante) as posicion_hash
FROM estudiantes
ORDER BY hash_estudiante(id_estudiante);


# Hash con PARTICIONES:

CREATE TABLE estudiantes_hash (
    ...
) PARTITION BY HASH (id_estudiante);

CREATE TABLE estudiantes_hash_p0 PARTITION OF estudiantes_hash 
	FOR VALUES WITH (MODULUS 4, REMAINDER 0);
...


# Inserción para poblar particiones:

INSERT INTO estudiantes_hash SELECT * FROM estudiantes;

4) MEDICIÓN DE RENDIMIENTO (EXPLAIN)

# Ejemplo clave:

EXPLAIN (ANALYZE, BUFFERS)
SELECT * FROM estudiantes_heap WHERE id_estudiante = 1077;


Ahí puedes ver sequential scan, y compararlo con la tabla ordenada.
