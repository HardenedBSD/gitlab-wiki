# paxlog TODO

## proto
void pax_log ( const char * format, ... )
{
if pax_log_system -> printf - dmesg message
}

void pax_ulog ( const char * format, ... )
{
if pax_log_user -> uprintf - user specific message
}

## sysctls

security.pax.log.user

security.pax.log.system

security.pax.log.floodtime

security.pax.log.floodburst

https://git.hardenedbsd.org/hardenedbsd/HardenedBSD/-/wikis/9%5D-grsec_and_pax_options_linux5.4#logging-options