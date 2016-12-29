MapProxy   MapProxy is an open source proxy for geospatial data. It caches, accelerates and transforms data from existing map services and serves any desktop or web GIS client.News2016-07-22: New MapProxy 1.9.0 release         2015-05-18: New MapProxy 1.8.0 release         2015-05-06: Web-based configuration for MapProxy         2014-07-07: New MapProxy 1.7.0 release         2013-09-05: New MapProxy 1.6.0 release         Features of MapProxy   Tile cacheMapProxy is a tile server (WMS-C, TMS, WMTS, KML SuperOverlays). It reads data from:WMS sources (1.0.0–1.3.0)TMS/WMTS sourcesMapserver and Mapnik configurationsany TileCache, Google Maps or Bing compatible sourceOther features:cache data on filesystem, as MBTiles or inside CouchDBreproject WMS sources to other SRSstores identical images just once (e.g. ocean tiles)embed watermark in tilesmerge multiple sourceslimit sources to polygon areasmany moreWMSMapProxy is also a full compliant WMS server and supports any WMS client (desktop and web). It supports WMS responses from cached data:merges tiles and scales or reprojects imagesaccelerates existing WMS 10 to 100 timesand cascaded WMS services:multi-threaded requestsmerges multiple sourcesadds transparency to opaque layersreprojects on-the-flyIt also supports combinations of cached and cascaded layers.Other features:respond to `GetLegendGraphic` requestscascade ``GetFeatureInfo`` requests with optional XSL transformationslimit sources to polygon areasconvert WMS versions and image formatssupport for non-image raster data like DEMsSecurityMapProxy comes with a flexible security API that allows you to add fine-grained control over services and layers. You can even restrict access of single layers to polygon extents.SeedingYou can pre-generate the tile cache for better performance – this is called seeding. Some unique features:fine-grained control over the seed area with Shapefiles, PostGIS or WKT geometriesmulti-threaded requests, meta-tile splitting and image encodingoptimized seeding strategy (to work _with_ your database cache)Other featuresYou can use MapProxy to upgrade your SDI without touching your existing servers:offer projections that your sources do not supportoffer protocol versions and image formats that your sources do not supportcombine multiple layers and servershide WMS serversadd watermarks and attribution lines to the imagesetc.MapProxy is standard compliant and works with the following open specifications:OGC WMS 1.0.0, 1.1.0, 1.1.1, 1.3.0OGC WMTS 1.0.0 (KVP and RESTful)OSGeo TMS 1.0.0OGC KML 2.2 SuperOverlaysGetting startedCurious? With the following four lines you can install MapProxy and all dependencies, create a new configuration and start MapProxy in development mode. After that you can visit .sudo aptitude install python-imaging python-yaml libproj0
sudo easy_install mapproxy
mapproxy-util create -t base-config ./
mapproxy-util serve-develop ./mapproxy.yaml
Instalasi MapserverTahapan instalasi mapserver dijelaskan di bab ini.+Centos 6Lakukan instalasi mapserver melalui repository elgis dengan menambahkan repository elgis terlebih dahulu, repo elgis sebelumnya juga membutuhkan repo epel+# rpm -Uvh http://download.fedoraproject.org/pub/epel/6/x86_64/epel-release-6-8.noarch.rpm
# rpm -Uvh http://elgis.argeo.org/repos/6/elgis-release-6-6_0.noarch.rpm
Terjadi kesalahan dalam library armadillo di epel. Exclude armadillo dari repo epel karena akan menyebabkan galat. edit file epel.repo tambahkan exclude=armadillo dan install armadillo melalui repo lainnya atau download manual.+# vi /etc/yum.repos.d/epel.repo
[epel]
name=Extra Packages for Enterprise Linux 6 - $basearch
#baseurl=http://download.fedoraproject.org/pub/epel/6/$basearch
mirrorlist=https://mirrors.fedoraproject.org/metalink?repo=epel-6&arch=$basearch
failovermethod=priority
enabled=1
gpgcheck=1
gpgkey=file:///etc/pki/rpm-gpg/RPM-GPG-KEY-EPEL-6
exclude=armadillo*
Instalasi armadillo+# wget http://elgis.argeo.org/repos/6/elgis/x86_64/gdal-1.9.2-4.el6.x86_64.rpm
# yum install armadillo-3.800.2-1.el6.x86_64.rpm
kemudian lanjutkan proses instalasi+# yum install gpsbabel
# yum install gdal
# yum install mapserver
# yum install glibc
# yum install libpng libpng-devel
# yum install gd gd-devel
# yum install giflib-devel
# yum install proj-epsg
# rpm -ql mapserver
# /usr/libexec/mapserver -v
Instalasi MapproxyMapproxy berfungsi untuk melakukan caching agar semua request tidak langsung diteruskan kepada mapserver. Hal ini dilakukan karena pentingnya menjaga performansi aplikasi agar load peta tidak terlalu lama. Langkah instalasi mapproxy:+# yum install python-pip python-devel
# pip install MapProxy
Selanjutnya adalah membuat file config map proxy di dalam direktori /var/mapproxy+# cd /var/
# mapproxy-util create -t base-config mymapproxy
Untuk kebutuhan akses mapproxy dibutuhkan port 8080 yang kita buka dari iptables atau firewall os dengan menggunakan perintah :+# iptables -I INPUT -p tcp --dport 8080 -j ACCEPT 
# service iptables save
Setting apache  (opsional)Langkah ini merupakan langkah pemanggilan mapserver melalui web server Apache, jika anda sudah menggunakan mapproxy atau seudah melakukan instalasi mapproxy maka anda tidak diperlukan lagi melakukan langkah instalasi apache ini. Apache web server membaca mapserver melalui CGI yang merupakan kepanjangan dari Common Gateway Interface, yaitu menjalankan file mapserver langsung melalui url HTTP .Maka sebelumnya anda diharuskan untuk melakukan instalasi apache dengan perintah :+# yum install httpd
buat softlink pada direktori web server atau virtual host yang sudah dibangun+# ln -s /usr/libexec/mapserver /var/www/cgi-bin/mapserv
# mkdir /var/www/html/tmp
# cp /usr/lib64/libmapserver.so /usr/lib64/php-zts/modules
arti sintak diatas adalah membuat softlink atau shotcut dari /usr/libexec/mapserver di direktori /var/www/cgi-bin/mapserv+Untuk apache, edit file /etc/httpd/conf/httpd.conf cari blok dari cgi-bin kita pastikan dari direktori tersebut bisa menjalankan cgi. Dengan melakukan perubahan pada blok Directory /var/www/cgi-bin+<Directory "/var/www/cgi-bin">
  Options Indexes FollowSymLinks ExecCGI
  AddHandler cgi-script sh
  AllowOverride None
  Order allow,deny
  Allow from all
</Directory>
Setelah itu simpan dan restart httpd dengan perintah +# service httpd restart
Untuk kebutuhan akses mapserver dibutuhkan port 80 yang kita buka dari iptables atau firewall os dengan menggunakan perintah :+# iptables -I INPUT -p tcp --dport 80 -j ACCEPT 
# service iptables save
jalankan url ini dibuka +http://192.168.1.253/cgi-bin/mapserv?map=/var/www/html/agm.map&SERVICE=WMS&VERSION=1.1.1&REQUEST=GetMap&LAYERS=roads&STYLES=&SRS=EPSG:4326&BBOX=94.5011475,-11.007385,141.01947,6.076721&WIDTH=1024&HEIGHT=768&FORMAT=image/png
maka akan keluar peta  Berarti anda sudah berhasil melakukan instalasi+Konfigurasi Mapserver dan MapProxyPersiapan DataBuat folder mapdata di /var terlebih dahulu kemudian pada direktori tersebut buat kembali direktori dengan nama shp, mapfile, tmp dan common. kemudian taruh file vektor shapefile peta kita di direktori /vaw/mapdata/shp. Dicontohkan disini adalah file peta Indonesia berupa kabupaten dan kota. Setelah itu buat mapfile dengan nama agm.map di direktori mapfile yang berisi :+map
  size 640 480
  imagetype png8
  imagecolor 225 225 225
  shapepath "../shp"
  fontset "../common/fonts/msfontset.txt"
  projection
    "init=epsg:4326"
  end
  debug on
  web
    #imagepath "/var/mapdata/tmp/"
    #imageurl "/demoms/tmp/"
    metadata
      wms_title "Awangga Geo Map"
      wms_srs "EPSG:4326"
      wms_enable_request "*"
    end
  end
  outputformat
    name "png256"
    driver "AGG/PNG"
    imagemode "pc256"
    extension "png"
  end
  outputformat
    name "png"
    driver "AGG/PNG"
    imagemode "rgba"
    extension "png"
    mimetype "image/png"
    imagemode RGBA
  end
  outputformat
    name "png8"
    driver "AGG/PNG"
    imagemode "rgba"
    extension "png"
    mimetype "image/png"
    imagemode RGBA
    transparent ON
    formatoption "QUANTIZE_FORCE=ON"
    formatoption "QUANTIZE_DITHER=ON"
    formatoption "QUANTIZE_COLORS=250"
  end
  outputformat
    name "jpg"
    driver "AGG/JPEG"
    extension "jpg"
    formatoption "QUALITY=85"
  end
  LAYER
    NAME base_map
    group roads
    TYPE POLYGON
    STATUS ON
    DATA "00"
    POSTLABELCACHE FALSE
    PROCESSING "LABEL_NO_CLIP=ON"
    LABELCACHE ON
    LABELITEM "KABKOT"
    CLASS
      #minscale 10000
      maxscale 500000
      Name "indonesia_kab"
      STYLE
        COLOR 102 255 102
        OUTLINECOLOR 200 200 200
        SYMBOL 0
      END
      LABEL
        COLOR  0 0 0
        OUTLINECOLOR 255 255 255
        FONT "FreeSans"
        TYPE truetype
        SIZE 8
        POSITION CC
        PARTIALS TRUE
        MINDISTANCE 50
        REPEATDISTANCE 9999
      END
    END
    METADATA
      "DESCRIPTION"   "Peta Indonesia"
    END
  END
end
Konfigurasi MapProxyEdit file /var/mymapproxy/mapproxy.yaml yang disesuaikan dengan konfigurasi dan data kita+services:
  demo:
  tms:
    use_grid_names: true
    # origin for /tiles service
    origin: 'nw'
  kml:
      use_grid_names: true
  wmts:
    # use restful access to WMTS
    restful: true
    # this is the default template for MapProxy
    restful_template: '/{Layer}/{TileMatrixSet}/{TileMatrix}/{TileCol}/{TileRow}.{Format}'
    # and also allow KVP requests
    kvp: true
    md:
      # metadata used in capabilities documents for WMTS
      # if the md option is not set, the metadata of the WMS will be used
      title: Awangga GeoMap
      abstract: This is the Awangga GeoMap.
      online_resource: http://www.awangga.net/
      contact:
        person: Rolly Maulana Awangga
        position: Software Engineer
        organization: Belant Persada
        address: Jl. Ligar Nyawang No.2
        city: Bandung
        postcode: 40191
        country: Indonesia
        phone: +62(0)813-12000-300
        fax: +62(0)813-12000-300
        email: rolly@awang.ga
      # multiline strings are possible with the right indention
      access_constraints:
        This service is intended for Sekretariat Negara Only.
        The data is under development on Sekretarian Negara Republik Indonesia.
        (http://setneg.go.id/)
      fees: 'None'
  wms:
    md:
      title: MapProxy WMS Proxy
      abstract: This is a minimal MapProxy example.

layers:
  - name: agm
    title: Awangga Geo Map - www.awangga.net
    sources: [agm_cache]

caches:
  agm_cache:
    grids: [webmercator]
    sources: [agm_source]

sources:
  agm_source:
    type: mapserver
    req:
      layers: roads
      map: /var/mapdata/mapfile/agm.map
    coverage:
      bbox: [94.5011475, -11.007385, 141.01947, 6.076721]
      srs: 'EPSG:4326'
    mapserver:
      binary: /usr/libexec/mapserver
      working_dir: /var/mapdata/tmp
    supported_srs: ['EPSG:4326']

grids:
    webmercator:
        base: GLOBAL_WEBMERCATOR

globals:
Setelah itu kita coba jalankan maproxy dengan perintah  :+# mapproxy-util serve-develop mapproxy.yaml -b 0.0.0.0
Kemudian kita buka demo nya di alamat : http://192.168.1.253:8080/demo/ kemudian masuk ke menu WMS atau WMTS PNG dimana 192.168.1.253 adalah alamat server yang kita install. + results matching ""No results matching ""
references :https://mapproxy.org/https://awangga.gitbooks.io/mapserver-untuk-pemula/content/chapter1.html
