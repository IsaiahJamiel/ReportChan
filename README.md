# LO-636 - Pengecheckan QO DC AHI Jababeka
Rangkuman berdasarkan kode untuk setiap jenis QO di menu Summary QO. Setiap jenis QO di konfigurasi di dalam ```scprd.[WMWHSE[N]].[rptQO_parameter]```.

- [LO-636 - Pengecheckan QO DC AHI Jababeka](#lo-636---pengecheckan-qo-dc-ahi-jababeka)
  - [1. Ketepatan waktu Receiving ASN](#1-ketepatan-waktu-receiving-asn)
  - [2. Ketepatan waktu Storing / Putaway](#2-ketepatan-waktu-storing--putaway)
  - [3. Ketepatan waktu proses picking GRW](#3-ketepatan-waktu-proses-picking-grw)
  - [4. Ketepatan waktu proses picking Flow Thru](#4-ketepatan-waktu-proses-picking-flow-thru)
  - [5. Ketepatan waktu pengiriman GRW - Dalam Kota](#5-ketepatan-waktu-pengiriman-grw---dalam-kota)
  - [6. Ketepatan waktu pengiriman GRW - Luar Kota](#6-ketepatan-waktu-pengiriman-grw---luar-kota)
  - [7. Ketepatan waktu pengiriman Flow Thru - Dalam kota](#7-ketepatan-waktu-pengiriman-flow-thru---dalam-kota)
  - [8. Ketepatan waktu pengiriman Flow Thru - Luar Kota](#8-ketepatan-waktu-pengiriman-flow-thru---luar-kota)
  - [9. Ketepatan pemenuhan quantity order (Fill Rate)](#9-ketepatan-pemenuhan-quantity-order-fill-rate)
  - [10. LPPB DO](#10-lppb-do)
  - [11. % Akurasi data stock barang di DC (Cycle count)](#11--akurasi-data-stock-barang-di-dc-cycle-count)
  - [12. Kerusakan Barang akibat handling DC](#12-kerusakan-barang-akibat-handling-dc)
  - [13. Produktivitas Picking GRW](#13-produktivitas-picking-grw)
  - [14. Produktivitas Picking Flow Thru](#14-produktivitas-picking-flow-thru)
  - [15. Akurasi data stock barang di DC (AHI \& AHE)	--total cycle count yang dilakukan di DC](#15-akurasi-data-stock-barang-di-dc-ahi--ahe--total-cycle-count-yang-dilakukan-di-dc)


## 1. Ketepatan waktu Receiving ASN
Dari tabel rpt_inboundKPI, perbandingan total order dibagi Jumlah order yang selisih ed0tdate dan adddate nya lebih kecil dari value yang di set di rptQO_parameter dengan key RCV1.

Rumus:
$$ Achievement = {Total \over {N * (EditDate - AddDate >= @DayLimit )}} $$
  *@DayLimit = [rptQO_parameter].[HARI]
(Total ASN di-received tepat waktu (max 1x24 jam) / Total ASN di-received) * 100%
## 2. Ketepatan waktu Storing / Putaway
Perhitungan diambil dari lama barang hasil receipt berpindah dari Staging Inbound ke Staging Departemen. KPI: 2 hari

Rumus:

$$
    OnTime = { ((DatePutAway - GetDate()) < KPI )  }  
$$

$$
    Achievement = { Ontime \over Total } * 100 
$$
(Total barang yang dipindahkan tepat waktu (2x24 jam) / Total LPN yang di-received) * 100%

## 3. Ketepatan waktu proses picking GRW
lama proses picking GRW (H+1). Diambil dari order dilakukan release sampai dengan status allocated/picked/pack

Membaca tabel ITRN, Order (tipe 56), dan Pick Detail
$$
    OnTime = { (SUM(PickingDetail.AddDate - (ITRN.AddDate)) < KPI )  }  
$$
   
$$
    Achievement = { Ontime \over Total } * 100 
$$
(Total quantity barang yang rusak / Total quantity stock pada akhir bulan) * 100%

## 4. Ketepatan waktu proses picking Flow Thru	
Lama proses picking flowthru by caseid (H+2)

Sama seperti tipe 3, namun tipe order 91

Membaca tabel ITRN, Order (tipe 91), dan Pick Detail
$$
    OnTime = { (SUM(PickingDetail.AddDate - (ITRN.AddDate)) < KPI )  }  
$$
   
$$
    Achievement = { Ontime \over Total } * 100 
$$

## 5. Ketepatan waktu pengiriman GRW - Dalam Kota
Membaca tabel [rpt_orderKPI] dengan tipe 56 dan status 95
KPI = 5 hari
Luar/Dalam kota dibedakan melalui tabel STORER kolom State.
Jika kolom state = null, maka akan dianggap DK
$$
    OnTime = { (SUM(Shippeddate - Requested Ship Date) < KPI )  }  
$$
   
$$
    Achievement = { Ontime \over Total } * 100 
$$
(Total transaksi pengiriman GRW yang tepat waktu (max 5x24 jam) / Total transaksi GRW) * 100%

## 6. Ketepatan waktu pengiriman GRW - Luar Kota
Membaca tabel [rpt_orderKPI] dengan tipe 56 dan status 95
KPI = 5 hari
Luar/Dalam kota dibedakan melalui tabel STORER kolom State. 
Jika kolom state = null, maka akan dianggap DK
$$
    OnTime = { (SUM(Shippeddate - Requested Ship Date) < KPI )  }  
$$
   
$$
    Achievement = { Ontime \over Total } * 100 
$$
(Total transaksi pengiriman GRW yang tepat waktu (max 5x24 jam) / Total transaksi GRW) * 100%

## 7. Ketepatan waktu pengiriman Flow Thru - Dalam kota    
Diambil dari lama CaseID terbentuk sampai dengan confirm Shipment.
Key = SHIPFTDK1

Data diambil dari tabel [rpt_orderKPI] dengan tipe 91

Dibedakan melalui [State] storer antara LK/DK
$$
    OnTime = { (SUM(Release date/Allocated - Shipped Date) < KPI )  }  
$$
Jika Release date belom di set, maka akan menggunakan allocated date
$$
    Achievement = { Ontime \over Total } * 100 
$$

(Total transaksi pengiriman FT yang tepat waktu (max 7x24 jam) / Total transaksi FT) * 100%

## 8. Ketepatan waktu pengiriman Flow Thru - Luar Kota
Diambil dari lama CaseID terbentuk sampai dengan confirm Shipment.
Key = SHIPFTDK1

Data diambil dari tabel [rpt_orderKPI] dengan tipe 91

Dibedakan melalui [State] storer antara LK/DK
$$
    OnTime = { (SUM(Release date / Allocated Date - Shipped Date) < KPI )  }  
$$
Jika Release date belom di set, maka akan menggunakan allocated date
$$
    Achievement = { Ontime \over Total } * 100 
$$
(Total transaksi pengiriman FT yang tepat waktu (max 5x24 jam) / Total transaksi FT) * 100%

## 9. Ketepatan pemenuhan quantity order (Fill Rate)
Key = FILLQO1
Membandingkan kuantitas ordered dan kuantitas pengiriman

$$
    Achievement = {{TotalShipped \over TotalQTY} * 100 }
$$
(Total quantity shipment / Total quantity order) * 100%

## 10. LPPB DO
Total LPPB DO yang terjadi di DC
Key = LPPB1

Komparasi QTY Receive dan QTY Shipped dari tabel [LPPB_H] dan [LPPB_D]

Total quantity diambil dari [TOTALSHIPPED] tabel [rpt_orderKPI] 

$$
    OnTime = {{ SUM(|QTY Receive - TotalQTY|) } }
$$

$$
    Achievement = {{OnTime \over TotalQTY} * 100 }
$$
(Total qty LPPB DO / Total quantity pengiriman barang ke store) * 100%

## 11. % Akurasi data stock barang di DC (Cycle count)
Diambil dari data tampungan yang ditarik per jam 1 pagi
Key = CC1

Total all = Total Fix + Total Miss

Data diambil dari tabel [AccurationinventoryDC]

$$
    Achievement = {{Total Fix \over Total All} * 100 }
$$

## 12. Kerusakan Barang akibat handling DC
Data diambil dari total Barang yang dipasang holdcode Damage dibagi dengan total inventory yang ada

Key = DAMAGE1

Data kerusakan diambil dari tabel [Support_HOLDTRN] 

Data Total diambil dari tabel [ITRN] yang tidak memiliki tipe data 'MV'
$$
    Achievement = {{Rusak \over Total Quantity} * 100 }
$$
(Total quantity barang yang rusak / Total quantity stock pada akhir bulan) * 100%

## 13. Produktivitas Picking GRW
Data diambil dari orders dengan tipe 56 dan ITRN dengan source type Picking dan trantype = 'MV'

key = PRODGRW1

Total akan menghitung jumlah CASEID berdasrkan order dan itrn.

Sedangkan total diambil adalah jumlah row dari tabel order dan itrn yang diambil

$$
    Achievement = {{Total \over (OnTime \times day KPI )} * 100 }
$$

(Total quantity barang hasil picking GRW / (Total jumlah picker*130)) * 100%

## 14. Produktivitas Picking Flow Thru
Data diambil dari orders dengan tipe 91 dan ITRN dengan source type Picking dan trantype = 'MV'

key = PRODGRW1

Total akan menghitung jumlah CASEID berdasrkan order dan itrn.

Sedangkan total diambil adalah jumlah row dari tabel order dan itrn yang diambil

$$
    Achievement = {{Total \over (OnTime \times day KPI )} * 100 }
$$

(Total quantity barang hasil picking FT / (Total jumlah picker*180)) * 100%

## 15. Akurasi data stock barang di DC (AHI & AHE)	--total cycle count yang dilakukan di DC
Mengambil data dari udsp_rptCycleCountV2

key = CC2

$$
    Achievement = {{Jumlah Perhitungan Akurat \over Jumlah perhitungan} * 100 }
$$
(Total penghitungan cycle count yang akurat / Total penghitungan yang dilakukan) * 100%




