## IngresosStockVentas
# Reporte de Ingresos Stock y Ventas Musimundo

> 2.	Por otro lado, sobre la tabla diaria de la que hablamos. Eso está ok. Pero necesitamos que sea en un reporte diferente ¿Podrá ser?
Acá lo que queremos ver son los “movimientos” diarios del stock. ¿Será posible registrar todos los movimientos del comercial? Ya no solo hablamos de Venta e Ingresos. Estaríamos hablando de “Egresos Comercial” e “Ingresos Comercial”

Stock Comercial = Libre Utilización + Pendiente a Centro + Transito y Traslado - Pendiente Desde Centro

Necesito: 
- Venta a costo de reposición.
- Stock Comercial al Inicio.

Formato:

| Fecha | Stock Comerial UN | Ingresos UN | Egresos UN | Operación UN | Stock Comerial $ | Ingresos $ | Egresos $ | Operación $|
| ----------- | ----------- | ----------- | ----------- | ----------- |----------- |----------- |----------- |----------- |

---


Pasos
1. SituacionGeneralStock = Importar archivo Z:\SituacionGeneralStock.xlsx

2. Movimientos = Importar tablas MKPF - MSEG
SELECT
  DISTINCT 
  "MSEG"."ANLN1",
  "MSEG"."ANLN2",
  "MSEG"."APLZL",
  "MSEG"."AUFNR",
  "MSEG"."AUFPL",
  "MKPF"."BKTXT",
  "MKPF"."BLDAT",
  "MSEG"."BPMNG",
  "MSEG"."BPRME",
  "MSEG"."BSTME",
  "MSEG"."BSTMG",
  "MKPF"."BUDAT",
  "MSEG"."BUKRS",
  "MSEG"."BWART",
  "MSEG"."BWTAR",
  "MSEG"."CHARG",
  "MKPF"."CPUDT",
  "MKPF"."CPUTM",
  "MSEG"."DMBTR",
  "MSEG"."EBELN",
  "MSEG"."EBELP",
  "MSEG"."ERFME",
  "MSEG"."ERFMG",
  "MSEG"."EXBWR",
  "MSEG"."EXVKW",
  "MSEG"."GRUND",
  "MSEG"."KDAUF",
  "MSEG"."KDEIN",
  "MSEG"."KDPOS",
  "MSEG"."KOSTL",
  "MSEG"."KUNNR",
  "MSEG"."KZBEW",
  "MSEG"."KZVBR",
  "MSEG"."KZZUG",
  "MSEG"."LBKUM",
  "MSEG"."LGORT",
  "MSEG"."LIFNR",
  "MSEG"."MATNR",
  "MSEG"."MAT_KDAUF",
  "MSEG"."MAT_KDPOS",
  "MKPF"."MBLNR",
  "MSEG"."MEINS",
  "MSEG"."MENGE",
  "MKPF"."MJAHR",
  "MSEG"."NPLNR",
  "MSEG"."PS_PSP_PNR",
  "MSEG"."RSNUM",
  "MSEG"."RSPOS",
  "MSEG"."SALK3",
  "MSEG"."SHKZG",
  "MSEG"."SOBKZ",
  "MKPF"."USNAM",
  "MKPF"."VGART",
  "MSEG"."VKWRT",
  "MSEG"."VPRSV",
  "MSEG"."WAERS",
  "MSEG"."WERKS",
  "MSEG"."WRF_CHARSTC1",
  "MSEG"."WRF_CHARSTC2",
  "MSEG"."WRF_CHARSTC3",
  "MKPF"."XABLN",
  "MSEG"."XAUTO",
  "MKPF"."XBLNR",
  "MSEG"."ZEILE",
  "MSEG"."MAA_URZEI",
  "MSEG"."XMACC"
FROM
  "MKPF" 
  INNER JOIN "MSEG" ON 
             "MKPF"."MANDT"="MSEG"."MANDT" 
        AND  "MKPF"."MBLNR"="MSEG"."MBLNR" 
        AND  "MKPF"."MJAHR"="MSEG"."MJAHR"
WHERE
  "MKPF"."MANDT"='300' AND "MSEG"."MATNR" IN ('000000000000107465') AND
  "MKPF"."BUDAT" BETWEEN '20211209' AND '20211209' AND "MSEG"."LGORT"='0001'


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

