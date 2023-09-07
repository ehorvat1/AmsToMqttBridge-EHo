<script>
    import { zeropad, monthnames, addHours } from './Helpers.js';

    export let timestamp;
    export let fullTimeColor;
//    export let offset;
// EHorvat disabled next lines
//    let showFull;
//    $:{
//        showFull = Math.abs(new Date().getTime()-timestamp.getTime()) < 300000;
//        if(!isNaN(offset))
//            addHours(timestamp, offset - ((timestamp.getHours() - timestamp.getUTCHours())%24));
//    }
</script>

<!--  EHorvat added Year seconds to all  .... added:   :${zeropad(timestamp.getSeconds())}   and full year:  .${timestamp.getFullYear()}-->
{#if Math.abs(new Date().getTime()-timestamp.getTime()) < 300000 }
{`${zeropad(timestamp.getDate())}. ${monthnames[timestamp.getMonth()]}.${timestamp.getFullYear()} ${zeropad(timestamp.getHours())}:${zeropad(timestamp.getMinutes())}:${zeropad(timestamp.getSeconds())}`}
{:else}
<span class="{fullTimeColor}">{`${zeropad(timestamp.getDate())}.${zeropad(timestamp.getMonth()+1)}.${timestamp.getFullYear()} ${zeropad(timestamp.getHours())}:${zeropad(timestamp.getMinutes())}:${zeropad(timestamp.getSeconds())}`}</span>
{/if}


