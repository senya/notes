# Tracing qmp commands

Two main trace point: `handle_qmp_command` and `monitor_qmp_respond`. Note that the latter is available since vz-7.16.

You have several options to enable tracing:

1. Permanently, edit libvirt xml

```bash
virsh edit VMNAME
```

Add at the end, exactly before last `</domain>` line:

```xml
  <commandline xmlns="http://libvirt.org/schemas/domain/qemu/1.0">
    <arg value='-trace'/>
    <arg value='handle_qmp_command'/>
    <arg value='-trace'/>
    <arg value='monitor_qmp_respond'/>
  </commandline>
```
If you see, that xml already contains something like
```xml
  <qemu:commandline>
    <qemu:arg value='-some-argument'/>
  </qemu:commandline>
```
follow this style and just add
```xml
    <qemu:arg value='-trace'/>
    <qemu:arg value='handle_qmp_command'/>
```

2. Temporarily, enable/disable trace point in running guest:

```bash
virsh qemu-monitor-command VMNAME '{"execute": "trace-event-set-state", "arguments": {"enable": true, "name": "handle_qmp_command"}}'
virsh qemu-monitor-command VMNAME '{"execute": "trace-event-set-state", "arguments": {"enable": true, "name": "monitor_qmp_respond"}}'
```

3. Permanently, edit Qemu code and recompile:

```diff
--- a/softmmu/vl.c
+++ b/softmmu/vl.c
@@ -2779,6 +2779,8 @@ void qemu_init(int argc, char **argv, char **envp)
     }
 
     /* second pass of option parsing */
+    trace_opt_parse("handle_qmp_command");
+    trace_opt_parse("monitor_qmp_respond");
     optind = 1;
     for(;;) {
         if (optind >= argc)
```
