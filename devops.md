# Devops

provjera memorije: `free -h`
ako nemaš rama a imaš diska, dodaj swap.

nxingx config: `/etc/nginx/sites-available`
remote_syslog - šalje logove na papertrail, konfiguracija: `/etc/log_files.yml`

Unicorn se kršio s:
`Operation not permitted @ rb_file_chown - /home/jugosluh/shared/log/unicorn.log`
U `unicorn.rb` user nije bio postavljen na "app" s kojim sam deployao iz mine (on je stvarao foldere i sve)

`hash -r` Očisti hash table s executablima (automatski se napravi kad se promijeni $PATH)
