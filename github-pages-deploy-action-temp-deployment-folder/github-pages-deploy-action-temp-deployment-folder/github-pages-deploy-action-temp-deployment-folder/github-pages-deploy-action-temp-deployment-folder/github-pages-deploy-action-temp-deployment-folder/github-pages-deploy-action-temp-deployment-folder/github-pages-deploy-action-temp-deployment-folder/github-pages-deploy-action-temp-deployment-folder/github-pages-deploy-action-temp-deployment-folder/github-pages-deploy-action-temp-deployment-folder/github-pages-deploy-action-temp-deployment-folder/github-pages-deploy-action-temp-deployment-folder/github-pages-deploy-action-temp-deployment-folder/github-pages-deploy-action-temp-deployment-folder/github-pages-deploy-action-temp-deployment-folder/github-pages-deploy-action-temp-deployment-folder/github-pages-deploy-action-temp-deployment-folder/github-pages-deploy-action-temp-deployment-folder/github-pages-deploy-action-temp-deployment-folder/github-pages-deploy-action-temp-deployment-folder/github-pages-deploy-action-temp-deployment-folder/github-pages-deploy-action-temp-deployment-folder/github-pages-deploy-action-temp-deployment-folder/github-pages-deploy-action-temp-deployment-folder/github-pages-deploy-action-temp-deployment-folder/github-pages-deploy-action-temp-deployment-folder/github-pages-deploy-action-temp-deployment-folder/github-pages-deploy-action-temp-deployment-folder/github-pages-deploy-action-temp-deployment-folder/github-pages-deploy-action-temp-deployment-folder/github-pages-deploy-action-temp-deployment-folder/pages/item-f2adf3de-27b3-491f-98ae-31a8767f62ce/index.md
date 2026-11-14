Pertanyaan
----------

Bagaimana cara melihat container yang aktif dan vhost nginx apa saja yang disajikannya? Apa isi `ls /data/compose/` (10 12 14 16 19 23 30 32 33 34 drupal drupal-darinfs drupal8 drupal8a)? Apa arti tabel Volumes container `drupal_web_server`?

1\. Cara cek container aktif & vhost-nya (home.pesanweb.my.id)
--------------------------------------------------------------

    # Project compose mana yang aktif
    ssh home.pesanweb.my.id "docker compose ls -a"
    #  → drupal811  running(6)  /data/compose/34/docker-compose.yml
    #  → root       running(1)  /root/portainer-compose.yaml
    
    # Container + project + dir kerja masing-masing
    docker ps --format '{{.Names}}\t{{.Label "com.docker.compose.project"}}\t{{.Label "com.docker.compose.project.working_dir"}}'
    
    # Port mapping container nginx
    docker port drupal_web_server
    #  → 80/tcp -> 0.0.0.0:8086   81/tcp -> 0.0.0.0:8087
    
    # Mount apa saja yang dipakai container (bind vs volume)
    docker inspect drupal_web_server --format '{{json .Mounts}}'
    
    # CARA PALING LENGKAP: dump seluruh config nginx yang benar-benar aktif
    docker exec drupal_web_server nginx -T | grep -E 'listen|server_name|root |fastcgi_pass'

Di mesin swarm (192.168.2.57) namanya service, bukan compose: `docker service ls`, `docker service ps <nama>`, `docker service inspect --format '{{json .Spec.TaskTemplate.ContainerSpec.Mounts}}' <nama>`.

2\. Hasil `nginx -T` — vhost aktif `drupal_web_server`
------------------------------------------------------

listen

server\_name

root

fastcgi\_pass

80 (host 8086)

localhost

/var/www/html/drupal11/web

drupal\_fpm:9000 (Drupal 11)

81 (host 8087)

localhost

/var/www/html2/drupal8/web

drupal\_fpm7:9000 (Drupal 8)

**Poin penting:** nginx di sini _bukan_ memilih berdasarkan domain — `server_name localhost` artinya catch-all. Yang membedakan domain adalah **port** (80 vs 81), dan pembeda domain di dalam Drupal 8 dilakukan oleh **multisite** lewat Host header (`sites/web.pesanweb.my.id/`, `sites/belajarweb.pesanweb.my.id/`). Jadi "vhost" sesungguhnya di mesin ini = 2 blok server berbasis port.

3\. Penjelasan isi `/data/compose/`
-----------------------------------

Bukti: `docker compose ls` hanya menampilkan **drupal811** (dari `/data/compose/34/`) + portainer. Sisanya bukan project aktif:

Folder

Status

Isi / fungsi

`34`

**AKTIF**

Project `drupal811` yang berjalan sekarang (drupal8-src, drupal11-src, nginx-conf, db-backup, drupal-data)

`10 12 14 16 19 23 30 32 33`

inaktif

Snapshot deployment lama (Des 2025), struktur serupa (drupal8-src, nginx-conf, db-backup…). Arsip/rollback — **jangan** dihapus asal

`drupal-darinfs`

inaktif

Snapshot paling lengkap (ada drupal8\_data, drupal8\_files, dump, images, import, private, snippets, template). Owner `nobody:nogroup` → hasil copy/rsync (kemungkinan dari NFS)

`drupal`, `drupal8`, `drupal8a`

—

Folder kosong

Pola penomoran = urutan deployment; tiap kali deploy baru dibuat folder nomor baru, folder lama ditinggalkan (bukan dihapus). Hanya folder yang ada di `docker compose ls` + `docker ps` label `working_dir`\-nya yang benar-benar jalan.

4\. Penjelasan tabel Volumes `drupal_web_server`
------------------------------------------------

Host/volume

Path di container

Jenis & guna

`/data/compose/34/nginx-conf`

/etc/nginx/conf.d

**bind** — config vhost. Edit di host, reload nginx, langsung berlaku

`/data/compose/34/drupal11-src`

/var/www/html/drupal11

**bind** — kode Drupal 11

`/data/compose/34/drupal8-src`

/var/www/html2/drupal8

**bind** — kode Drupal 8 + semua folder multisite (`web/sites/*`)

`drupal811_drupal11_files_volume`

/var/www/html/drupal11/web/sites/default/files

**named volume** — upload D11. Terpisah dari kode → aman saat ganti/update source

`drupal811_drupal8_files_volume`

/var/www/html2/drupal8/web/sites/default/files

**named volume** — upload D8 situs default

Volume named lengkap di mesin ini: `drupal811_drupal8_files_volume`, `drupal811_drupal11_files_volume`, `drupal811_drupal11_config_sync_volume` (sync config D11), `drupal811_drupal_db_data` (data MariaDB).

**Catatan penting:** files untuk situs multisite (`web.pesanweb.my.id`, `belajarweb.pesanweb.my.id`) _bukan_ di named volume — ikut bind mount `drupal8-src` di host: `/data/compose/34/drupal8-src/web/sites/<domain>/files/`. Hanya situs `default` yang files-nya pakai named volume.

5\. Ringkasan ekosistem dua mesin
---------------------------------

home.pesanweb.my.id

192.168.2.57

Orkestrator

docker compose (project `drupal811`, dir `/data/compose/34`)

docker swarm (stack `drupal-nfs`, dir `/data/compose/98`)

Routing domain

Port host 8086/8087 + multisite Drupal

Nginx Proxy Manager (tabel `proxy_host`) → 8087 → container :81

Situs D8

web.pesanweb.my.id (DB drupal8)

belajarweb.pesanweb.my.id (DB belajarweb)