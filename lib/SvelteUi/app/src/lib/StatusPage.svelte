<script>
    import { metertype, boardtype, isBusPowered, getResetReason } from './Helpers.js';
    import { getSysinfo, gitHubReleaseStore, sysinfoStore } from './DataStores.js';
    import { upgrade, getNextVersion, upgradeWarningText } from './UpgradeHelper';
    import DownloadIcon from './DownloadIcon.svelte';
    import { Link } from 'svelte-navigator';
    import Clock from './Clock.svelte';
    import Mask from './Mask.svelte';
  
    export let data;
    export let sysinfo;

    let cfgItems = [{
        name: 'WiFi',
        key: 'iw'
    },{
        name: 'MQTT',
        key: 'im'
    },{
        name: 'Web',
        key: 'ie'
    },{
        name: 'Meter',
        key: 'it'
    },{
        name: 'Thresholds',
        key: 'ih'
    },{
        name: 'GPIO',
        key: 'ig'
    },{
        name: 'NTP',
        key: 'in'
    },{
        name: 'Price API',
        key: 'is'
    }];
  
    let nextVersion = {};
    gitHubReleaseStore.subscribe(releases => {
      nextVersion = getNextVersion(sysinfo.version, releases);
      if(!nextVersion) {
        nextVersion = releases[0];
      }
    });
 
    function askUpgrade() {
        if(confirm('Do you want to upgrade this device to ' + nextVersion.tag_name + '?')) {
            if((sysinfo.board != 2 && sysinfo.board != 4 && sysinfo.board != 7) || confirm(upgradeWarningText(boardtype(sysinfo.chip, sysinfo.board)))) {
                sysinfoStore.update(s => {
                    s.upgrading = true;
                    return s;
                });
                upgrade(nextVersion.tag_name);
            }
        }
    }

    async function reboot() {
      const response = await fetch('/reboot', {
            method: 'POST'
        });
        let res = (await response.json())
    }

    const askReboot = function() {
      if(confirm('Are you sure you want to reboot the device?')) {
        sysinfoStore.update(s => {
            s.booting = true;
            return s;
        });
        reboot();
      }
    }

    let firmwareFileInput;
    let firmwareFiles = [];
    let firmwareUploading = false;

    let configFileInput;
    let configFiles = [];
    let configUploading = false;

    getSysinfo();
</script>

<div class="grid xl:grid-cols-5 lg:grid-cols-3 md:grid-cols-2">
    <div class="cnt">
        <strong class="text-sm">Device information</strong>
        <div class="my-2">
            Chip: {sysinfo.chip}
        </div>
        <div class="my-2">
            Device: <Link to="/vendor">{boardtype(sysinfo.chip, sysinfo.board)}</Link>
        </div>
        <div class="my-2">
            MAC: {sysinfo.mac}
        </div>
        {#if sysinfo.apmac && sysinfo.apmac != sysinfo.mac}
        <div class="my-2">
            AP MAC: {sysinfo.apmac}
        </div>
        <div class="my-2">
            Last boot:
            {#if data.u > 0}
            <Clock timestamp={new Date(new Date().getTime() - (data.u * 1000))} fullTimeColor="" />
            {:else}
            -
            {/if}
        </div>
        <div class="my-2">
            Reason: {getResetReason(sysinfo)} ({sysinfo.boot_reason}/{sysinfo.ex_cause})
        </div>
        {/if}
        <div class="my-2">  <!--  EHorvat disabled consent stuff
            <Link to="/consent">
                <span class="btn-pri-sm">Update consents</span>
            </Link> -->
            <button on:click={askReboot} class="text-xs py-1 px-2 rounded bg-yellow-500 text-white mr-3 float-right">Neustart</button>
        </div> 
     </div>
    {#if sysinfo.meter}
    <div class="cnt">
        <strong class="text-sm">Meter</strong>
        <div class="my-2">
            Manufacturer: {metertype(sysinfo.meter.mfg)}
        </div>
        <div class="my-2">
            Model: {sysinfo.meter.model}
        </div>
        <div class="my-2">
            ID: {sysinfo.meter.id}
        </div>
    </div>
    {/if}
    {#if sysinfo.net}
    <div class="cnt"><!-- EHorvat next line was :   Network -->
        <strong class="text-sm">Netzwerk</strong>
        <div class="my-2">
            IP: {sysinfo.net.ip}
        </div>
        <div class="my-2">
            Mask: {sysinfo.net.mask}
        </div>
        <div class="my-2">
            Gateway: {sysinfo.net.gw}
        </div>
        <div class="my-2">
            DNS: {sysinfo.net.dns1} {#if sysinfo.net.dns2}/ {sysinfo.net.dns2}{/if}
        </div>
    </div>
    {/if}
    <div class="cnt">
        <strong class="text-sm">Firmware</strong>
        <div class="my-2"><!-- EHorvat next line was :   Installed version: {sysinfo.version} -->
            Installierte Version: 2.2.21-EHo-ge
        </div>        
        <div class="my-2"><!-- EHorvat new info-->
            Datum der Firmware: 9. Aug. 2023
        </div>
<!--  EHorvat disabled consent stuff  
        {#if sysinfo.upgrade.t && sysinfo.upgrade.t != sysinfo.version}
        <div class="my-2">
            <div class="bd-yellow">Previous upgrade attempt ({sysinfo.upgrade.t}) does not match current version ({sysinfo.version}) [{sysinfo.upgrade.x}/{sysinfo.upgrade.e}]</div>
        </div>
        {/if}


        {#if nextVersion}
            <div class="my-2 flex">
                Latest version: 
                <a href={nextVersion.html_url} class="ml-2 text-blue-600 hover:text-blue-800" target='_blank' rel="noreferrer">{nextVersion.tag_name}</a>
                {#if (sysinfo.security == 0 || data.a) && sysinfo.fwconsent === 1 && nextVersion && nextVersion.tag_name != sysinfo.version}
                <div class="flex-none ml-2 text-green-500" title="Install this version">
                    <button on:click={askUpgrade}><DownloadIcon/></button>
                </div>
                {/if}
            </div>
            {#if sysinfo.fwconsent === 2}
            <div class="my-2">
                <div class="bd-yellow">You have disabled one-click firmware upgrade, link to self-upgrade is disabled</div>
            </div>
            {/if}
        {/if} -->
        {#if (sysinfo.security == 0 || data.a) && isBusPowered(sysinfo.board) }
        <div class="bd-red">
            {upgradeWarningText(boardtype(sysinfo.chip, sysinfo.board))}
        </div>
        {/if}
        {#if sysinfo.security == 0 || data.a}
        <div class="my-2 flex">
            <form action="/firmware" enctype="multipart/form-data" method="post" on:submit={() => firmwareUploading=true} autocomplete="off">
                <input style="display:none" name="file" type="file" accept=".bin" bind:this={firmwareFileInput} bind:files={firmwareFiles}>
                {#if firmwareFiles.length == 0}<!-- EHorvat next line was :   translated Select Firmware file for upgrade -->
                <button type="button" on:click={()=>{firmwareFileInput.click();}} class="btn-pri-sm float-right">Firmware File ausw&auml;hlen</button>
                {:else}
                File: "{firmwareFiles[0].name}" <!-- EHorvat next line was :   <button type="submit" class="btn-pri-sm float-right">Upload</button>  also add btn-pri-sm2 to app.postcss -->
                <button type="submit" class="btn-pri-sm2 float-right">Jetzt installieren</button>
                {/if}
            </form>
        </div>
        {/if}
    </div>
    {#if sysinfo.security == 0 || data.a}
    <div class="cnt"><!-- EHorvat next line was :   Backup & restore -->
        <strong class="text-sm">Konfig sichern & wiederherstellen</strong>
        <form method="get" action="/configfile.cfg" autocomplete="off">
            <div class="grid grid-cols-2">
                {#each cfgItems as el}
                    <label class="my-1 mx-3"><input type="checkbox" class="rounded" name="{el.key}" value="true" checked/> {el.name}</label>
                {/each}<!-- EHorvat next line was :   translated Include Secrets -->
                <label class="my-1 mx-3 col-span-2"><input type="checkbox" class="rounded" name="ic" value="true"/> Mit Passw&ouml;rtern<br/><small>(SSID, PSK, passwords and tokens)</small></label>
            </div>
            {#if configFiles.length == 0}<!-- EHorvat next line was :   Download -->
            <button type="submit" class="btn-pri-sm float-right">Herunterladen</button>
            {/if}
        </form>
        <form action="/configfile" enctype="multipart/form-data" method="post" on:submit={() => configUploading=true} autocomplete="off">
            <input style="display:none" name="file" type="file" accept=".cfg" bind:this={configFileInput} bind:files={configFiles}>
            {#if configFiles.length == 0}<!-- EHorvat next line was :   Select file...< -->
            <button type="button" on:click={()=>{configFileInput.click();}} class="btn-pri-sm">W&auml;hle Konfig. File...</button>
            {:else}
            {configFiles[0].name}<!-- EHorvat next line was :   Upload -->
            <button type="submit" class="btn-pri-sm2">Jetzt hochladen</button>
            {/if}
        </form>
    </div>
    {/if}
</div><!-- EHorvat next line was :   Uploading firmware, please wait -->
<Mask active={firmwareUploading} message="Firmware wird gerade hochgeladen, dauert ca. 30 Sekunden. Das Browserfenster nicht schliessen"/>
<Mask active={configUploading} message="Konfiguration wird geladen, bitte warten ...."/>
