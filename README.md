# pj-media-neg

_Quick and dirty fix_ for media negotiation handling pending official solutions from pjsip.

German mobile providers are offering telephone-events/16000 and telephone-events/8000 with the same id (101) starting March 2021. Pjsip is using the first entry only, so the negotiation ends up not using telephone-events at all. This results in not being able to use DTMF tones for that connections.

The code in this repo should be seen as a temporary solution, there are more then one way to solve this problem but at least this approach does not require backend changes.

A sample INVITE is included in the issue https://github.com/pjsip/pjproject/issues/2675

**Activation**   
The media_neg_module in this repo is implemented as a pjsip module meaning no code patching is necessary. There are many ways you can activate it and in the end it comes down to how your project is setup, weather you use bindings to another language or pjsua2 as well as how you manage connectivity changes. The example below is what I use and it is based on a C layer on top of pjsua, you might have to adapt to your specific situation.

Register the module when starting up pjsip, normally after pjsua_start has returned with PJ_SUCCESS.
```c++
#if defined(PJ_HAS_IPV6) && PJ_HAS_IPV6 == 1
    pj_enable_media_negotiation_module();
#endif
```
Once registration completes, all occurrences of telephone-event/16000 are replaced by telephone-event/8000 for all incoming INVITEs.


Note:
You can not register the module from inside the registration callback since at that time the PJSUA_MUTEX is held by the stack and you will end up with a deadlock.
