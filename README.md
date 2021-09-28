# MySql-example

1* Name and height of the tallest bear killed by battalion 1 / Nombre y altura del oso mas alto que haya matado el batallón 1

SELECT osos.nombre, osos.altura from osos,muertes WHERE osos.id = muertes.id_osos AND causa = 'Batallón 1' AND osos.altura = (SELECT MAX(altura) from osos)

2* Name and height of the bears that have not been killed yet, along with the most recent sighting ordered by height / Nombre y altura de los osos que aún no han sido asesinados, junto con el avistamiento más reciente ordenado por altura.

SELECT id_osos,MAX(fecha) nfecha, osos.nombre, osos.altura FROM avistamientos,(SELECT * from osos WHERE not EXISTS (select DISTINCT muertes.id_osos from muertes WHERE osos.id = muertes.id_osos ORDER BY muertes.id_osos ASC)) AS osos WHERE osos.id = avistamientos.id_osos GROUP BY id_osos

3* List of bears that have been seen more than once in the last year / Lista de osos que se han visto más de una vez en el último año

SELECT COUNT(fecha) AS avistamientos, avistamientos.id_osos, osos.nombre FROM avistamientos,osos WHERE avistamientos.id_osos=osos.id GROUP BY id_osos HAVING COUNT(fecha)>1

4* List of resources that have been used (resource, quantity, unit)) in killing small bears (<= 5 meters) / Lista de recursos que se han usado (recurso, cantidad, unidad) en matar osos pequeños (<=5 metros)

SELECT SUM(movimientos_recursos.cantidad) AS cantidad, recursos.nombre, recursos.unidad FROM muertes,osos,movimientos_recursos,recursos WHERE osos.altura<=5 AND osos.id = muertes.id_osos AND muertes.id = movimientos_recursos.id_muerte AND recursos.id = movimientos_recursos.id_recurso GROUP BY recursos.nombre

5* List of bears with inconsistencies between their deaths and their sightings / Lista de los osos con incongruencias entre sus muertes y sus avistamientos

select id_osos,ultimo_avistamiento,fecha_de_muerte from (SELECT avistamientos.id_osos as id_osos, MAX(avistamientos.fecha) ultimo_avistamiento, muertes.id_osos as muerteoso, muertes.fecha as fecha_de_muerte from avistamientos, muertes WHERE muertes.id_osos = avistamientos.id_osos GROUP BY avistamientos.id_osos) as comparar where ultimo_avistamiento != fecha_de_muerte
