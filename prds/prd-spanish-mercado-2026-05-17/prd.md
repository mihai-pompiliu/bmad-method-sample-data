---
title: "MercadoVecino: Marketplace Hiperlocal a Pie de Cuadra"
status: borrador
created: 2026-05-17
owner: Equipo de Producto MercadoVecino
version: 0.3
---

# MercadoVecino: PRD v0.3

## Resumen ejecutivo

MercadoVecino es una aplicacion movil de marketplace hiperlocal disenada para el mercado mexicano. A diferencia de Facebook Marketplace o Mercado Libre, MercadoVecino restringe todas las transacciones a una distancia maxima de cinco cuadras (aproximadamente 400 metros) entre comprador y vendedor. El producto busca recuperar la confianza vecinal que existia en la vecindad tradicional y en el tianguis de barrio, ahora mediada por una capa digital ligera que respeta el modo en que los vecinos ya intercambian objetos, favores y fiado.

El lanzamiento inicial cubre tres colonias piloto de la Ciudad de Mexico (Roma Norte, Condesa y Coyoacan Centro), con expansion planeada a Guadalajara (colonia Americana) y Monterrey (colonia Del Valle, San Pedro) en el trimestre siguiente. La aplicacion soporta espanol mexicano como idioma principal, con opciones de interfaz en Nahuatl y Maya yucateco para colonias y municipios con presencia significativa de hablantes (por ejemplo, Milpa Alta y zonas conurbadas).

## Problema

Los marketplaces existentes en Mexico (Facebook Marketplace, Mercado Libre, Segundamano) no estan disenados para transacciones a pie. Esto genera tres problemas concretos:

1. **Friccion logistica**: vendedores y compradores deben coordinar traslados en automovil, Uber o transporte publico para articulos que valen 200 o 300 pesos, lo cual no justifica el costo ni el tiempo.
2. **Desconfianza entre desconocidos**: las transacciones ocurren entre personas que viven a kilometros de distancia, sin contexto vecinal compartido, lo que aumenta fraudes y cancelaciones de ultimo momento.
3. **Perdida del intercambio vecinal**: practicas tradicionales como prestar, regalar, fiar o intercambiar entre vecinos de la misma vecindad o cuadra no tienen una capa digital que las respalde sin convertirlas en una transaccion fria.

Adicionalmente, los hablantes de lenguas indigenas en zonas urbanas y periurbanas estan practicamente excluidos de los marketplaces digitales actuales, que asumen alfabetizacion en espanol escrito.

## Usuarios objetivo

### Persona principal: Lupita, vecina de Roma Norte

Lupita tiene 42 anos, vive en un edificio de seis departamentos en Roma Norte, trabaja desde casa como disenadora freelance. Tiene articulos del hogar que ya no usa (una licuadora, libros, ropa de su hija) y prefiere regalarlos o venderlos barato a alguien cercano antes que coordinar entregas por toda la ciudad. Confia mas en sus vecinos de la cuadra que en desconocidos de otra alcaldia. Paga regularmente con SPEI y CoDi desde su banca movil.

### Persona secundaria: Don Rafa, comerciante del tianguis digital

Don Rafa tiene 58 anos, opera un puesto pequeno de articulos de cocina en el tianguis dominical de la colonia. Quiere extender sus ventas al resto de la semana sin pagar comisiones altas de Mercado Libre ni manejar paqueteria. Busca vender solo a clientes que puedan recoger a pie.

### Persona terciaria: Yatzil, hablante de Nahuatl en Milpa Alta

Yatzil tiene 34 anos, es bilingue Nahuatl-espanol y prefiere interactuar en Nahuatl con sus vecinos del pueblo originario. Necesita una interfaz que respete su idioma sin tratarlo como traduccion de segunda clase.

### Anti-persona: el revendedor de otra alcaldia

Persona que vive en Iztapalapa pero quiere publicar para vender en Polanco, recoger con automovil y revender en otra zona. MercadoVecino esta explicitamente disenado para que esta persona no pueda operar en la plataforma. Las restricciones de verificacion de domicilio y radio de cinco cuadras existen precisamente para impedir este patron.

## Requerimientos funcionales

### Descubrimiento y geolocalizacion

- **RF-1**: La aplicacion debe mostrar unicamente publicaciones dentro de un radio de cinco cuadras (configurable entre 3 y 7 cuadras por el usuario, nunca mayor) desde la ubicacion verificada del usuario.
- **RF-2**: La verificacion de domicilio se hara mediante una combinacion de geolocalizacion repetida (tres confirmaciones en dias distintos desde el mismo punto) y, opcionalmente, validacion por vecino ya verificado mediante codigo QR fisico.
- **RF-3**: La aplicacion debe permitir filtrar publicaciones por colonia, no solo por radio, para usuarios que viven cerca de fronteras entre colonias (por ejemplo, Roma Norte y Roma Sur).

### Publicacion y categorias

- **RF-4**: Los usuarios podran publicar articulos en cuatro modos: **venta**, **prestamo**, **regalo** y **fiado**. El modo fiado solo estara disponible entre usuarios que ya hayan completado al menos una transaccion previa entre ellos.
- **RF-5**: Cada publicacion debe incluir precio en MXN (cuando aplique), descripcion en texto, hasta seis fotografias y categoria. Las categorias iniciales son: cocina y hogar, ropa y calzado, libros, electronica menor, juguetes, plantas, herramientas, y otros.
- **RF-6**: El tier "tianguis digital" para vendedores verificados como negocio formal permitira publicar hasta 50 articulos activos simultaneos por una cuota mensual de 99 MXN. El tier personal permite hasta 8 articulos activos sin costo.

### Pagos

- **RF-7**: La aplicacion debe soportar tres metodos de pago: **SPEI** (transferencia interbancaria), **CoDi** (cobro digital de Banxico mediante QR) y **efectivo al recoger**. La integracion con OXXO Pay se evaluara para v1.1.
- **RF-8**: Para transacciones menores a 500 MXN, el metodo predeterminado sugerido sera CoDi o efectivo. Para transacciones mayores, SPEI con confirmacion en la aplicacion antes de la entrega.
- **RF-9**: La aplicacion no retiene fondos en ningun momento (no es custodio). Las transferencias ocurren banco a banco; MercadoVecino solo registra que la transaccion fue confirmada por ambas partes.

### Confianza y reputacion

- **RF-10**: Cada usuario tendra un perfil con tres senales: numero de transacciones completadas en la colonia, antiguedad como vecino verificado, y reputacion (1 a 5 estrellas) otorgada solo por contrapartes de transacciones reales.
- **RF-11**: Los usuarios podran reportar publicaciones o vecinos por fraude, articulos prohibidos o conducta inapropiada. Los reportes seran revisados por un moderador humano dentro de 24 horas habiles.
- **RF-12**: Un vecino con tres reportes confirmados por moderacion sera suspendido por 30 dias. Cinco reportes confirmados resultan en expulsion permanente de la colonia, con posibilidad de apelacion por escrito ante el equipo de confianza.

### Idiomas y accesibilidad

- **RF-13**: La interfaz estara disponible en espanol mexicano (predeterminado), Nahuatl (variante central) y Maya yucateco. La seleccion de idioma sera por usuario, no por colonia, aunque se sugerira automaticamente segun ubicacion.
- **RF-14**: Los titulos y descripciones de las publicaciones podran ingresarse en cualquiera de los tres idiomas. La aplicacion ofrecera traduccion opcional al espanol para que vecinos de otras lenguas puedan descubrir la publicacion, sin sobrescribir el texto original.
- **RF-15**: La aplicacion soportara lectura de voz (text-to-speech) en los tres idiomas para usuarios con baja alfabetizacion en lectura. Las grabaciones de voz como descripcion de publicacion estaran permitidas hasta por 30 segundos.

## Requerimientos no funcionales

- **RNF-1 (rendimiento)**: La aplicacion debe cargar el feed inicial de publicaciones en menos de 2 segundos en redes 4G tipicas de la Ciudad de Mexico.
- **RNF-2 (privacidad)**: La ubicacion exacta del usuario nunca se mostrara a otros usuarios. Solo se mostrara la colonia y una distancia aproximada en cuadras.
- **RNF-3 (cumplimiento)**: La plataforma debera cumplir con la Ley Federal de Proteccion al Consumidor y mantener canal abierto con PROFECO para atencion de quejas formales. Datos personales bajo la LFPDPPP.
- **RNF-4 (offline parcial)**: La aplicacion debe permitir consultar publicaciones ya cargadas sin conexion (lectura) y encolar mensajes para envio cuando se recupere conectividad.
- **RNF-5 (plataformas)**: iOS 15 o superior, Android 9 o superior. Web responsivo se evaluara para v1.2.

## No-objetivos

Para evitar dispersion en v1, MercadoVecino explicitamente **no** abordara lo siguiente:

- Envios a domicilio, paqueteria o integracion con DHL, Estafeta, 99minutos o similares. Todo es a pie.
- Transacciones entre colonias o municipios. Si un vecino se muda, debe re-verificar su nuevo domicilio.
- Servicios profesionales (plomeros, ninera, clases). El alcance es solo objetos fisicos.
- Compraventa de automoviles, bienes raices, animales vivos, alcohol, tabaco, medicamentos o cualquier articulo regulado por COFEPRIS o por la Secretaria de Salud.
- Funcionar como custodio de fondos o procesar pagos directamente. No somos institucion financiera.
- Soporte para mas de tres idiomas en v1. Otras lenguas (Zapoteco, Mixteco, Otomi) se evaluaran segun demanda real por colonia en v1.x.

## Metricas de exito

Los siguientes indicadores se medirAn a 90 dias post-lanzamiento en las tres colonias piloto:

- **Adopcion**: 1,500 usuarios verificados por colonia piloto (4,500 en total).
- **Actividad**: al menos 40% de usuarios verificados con una publicacion activa o una transaccion completada en los ultimos 30 dias.
- **Transacciones a pie**: 95% o mas de las transacciones reportadas como completadas ocurrieron sin uso de automovil o transporte motorizado (encuesta posterior a la transaccion).
- **Confianza**: calificacion promedio de contrapartes igual o mayor a 4.3 sobre 5.
- **Diversidad linguistica**: al menos 200 usuarios activos usando interfaz en Nahuatl o Maya yucateco al cierre del trimestre.
- **Salud comercial del tier tianguis digital**: 80 negocios verificados pagando suscripcion mensual al cierre de los 90 dias.

## Supuestos y preguntas abiertas

### Supuestos

- La densidad poblacional de las colonias piloto es suficiente para que un radio de cinco cuadras contenga inventario relevante (validado parcialmente con encuesta vecinal en marzo 2026).
- SPEI y CoDi tienen penetracion suficiente entre nuestros usuarios objetivo (CONDUSEF reporta adopcion superior al 60% en zonas urbanas).
- Los hablantes de lenguas indigenas en zonas urbanas tienen smartphones compatibles (a confirmar mediante levantamiento en Milpa Alta).

### Preguntas abiertas

1. ¿Como manejar el caso de un vecino que se muda dentro de la misma colonia? ¿Re-verificacion completa o validacion ligera?
2. ¿Que sucede con la reputacion acumulada si un vecino cambia de colonia? Propuesta inicial: la reputacion viaja con el usuario, pero las transacciones se contabilizan por colonia.
3. ¿Conviene asociarnos con PROFECO desde el inicio para un canal formal de quejas, o esperar a tener volumen?
4. ¿El modo "fiado" requiere algun aviso legal explicito para no caer en regulacion de credito al consumo bajo la CNBV?
5. ¿Las cuotas del tier tianguis digital (99 MXN/mes) son sostenibles, o conviene un modelo por transaccion para vendedores pequenos?
6. ¿Como validar variantes regionales de Nahuatl sin caer en un sesgo de la variante central? Posible colaboracion con INALI.

## Apendice: glosario breve

- **Cuadra**: manzana urbana, aproximadamente 80 metros lineales en ciudades mexicanas tipicas.
- **Colonia**: subdivision administrativa de una alcaldia o municipio; equivalente cultural al barrio.
- **Vecindad**: edificio o conjunto de viviendas alrededor de un patio comun, tradicional en el centro de la Ciudad de Mexico.
- **Tianguis**: mercado al aire libre, rotativo, tipicamente semanal.
- **Fiado**: practica informal de credito entre vecinos o comerciantes de confianza, sin contrato escrito.
- **SPEI**: Sistema de Pagos Electronicos Interbancarios, operado por Banxico.
- **CoDi**: Cobro Digital, esquema de pago QR de Banxico sin comisiones.
- **PROFECO**: Procuraduria Federal del Consumidor.
