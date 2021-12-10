## IngresosStockVentas
# Reporte de Ingresos Stock y Ventas Musimundo

> 2.	Por otro lado, sobre la tabla diaria de la que hablamos. Eso está ok. Pero necesitamos que sea en un reporte diferente ¿Podrá ser?
Acá lo que queremos ver son los “movimientos” diarios del stock. ¿Será posible registrar todos los movimientos del comercial? Ya no solo hablamos de Venta e Ingresos. Estaríamos hablando de “Egresos Comercial” e “Ingresos Comercial”

Stock Comercial = Libre Utilización + Pendiente a Centro + Transito y Traslado - Pendiente Desde Centro

Necesito: 
- Venta a costo de reposición.
- Stock Comercial al Inicio.

Formato:

| Fecha | Ingresos UN | Stock Comerial UN | Venta UN | Operación UN | Ingresos $ | Stock Comerial $ | Venta $ | Operación $|
| ----------- | ----------- | ----------- | ----------- | ----------- |----------- |----------- |----------- |----------- |

---


Pasos
1. IngresosEgresosTMP = Importar tablas MKPF - MSEG - EKPO - EKKO - MARA - LFA1
   # MKPF AS T00001:Cabecera de doc.artículo
    Campos:
        MBLNR MJAHR VGART BLDAT BUDAT CPUDT USNAM XBLNR LE_VBELN
    Filtros:
        CPUDT BETWEEN %v_Desde% %v_Hasta%
        VGART = WE

   # MSEG AS T00002:Segmento doc.material
    Campos:
        MBLNR MJAHR ZEILE LINE_ID BWART MATNR WERKS LGORT LIFNR DMBTR MENGE EBELN EBELP GJAHR BUKRS BSTMG
    Filtros:
        BWART IN (101,102)
        WERKS LIKE M*
    
   # EKPO AS T00004:Posición del documento de compras
    Campos:
        EBELN EBELP MATNR TXZ01 WERKS MENGE MTART

   # EKKO AS T00005:Cabecera del documento de compras
    Campos:
        EBELN BUKRS BSTYP BSART AEDAT LIFNR

   # MARA AS T00007:Datos generales artículo
    Campos:
    MATNR MTART BRAND_ID MATKL VOLUM
    
   # LFA1 AS T00011:Maestro de proveedores (parte general)
    Campos:
        LIFNR NAME1
    
    Uniones:
        T00001.MBLNR=T00002.MBLNR
        T00001.MJAHR=T00002.MJAHR
        T00002.EBELN=T00004.EBELN
        T00002.EBELP=T00004.EBELP
        T00004.EBELN=T00005.EBELN
        T00004.MATNR=T00007.MATNR
        T00005.LIFNR=T00011.LIFNR

