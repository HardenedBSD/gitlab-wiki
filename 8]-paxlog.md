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

http://en.wikibooks.org/wiki/Grsecurity/Appendix/Grsecurity_and_PaX_Configuration_Options#Logging_Options