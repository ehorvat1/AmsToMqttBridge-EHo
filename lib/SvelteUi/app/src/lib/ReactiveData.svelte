<script>
    export let last_Counter_Info;
    export let last_Counter_Value;
    export let last_Counter_Costs;    
    export let last_Counter_Diff;
    export let last_expCounter_Info;
    export let last_expCounter_Value;
    export let last_expCounter_Costs;    
    export let last_expCounter_Diff;
    export let hasExport;
</script>

<div class="mx-2 text-sm">
    <strong>Last energy bill</strong>
    <br/><br/>
    <strong>Import</strong>
    <div class="grid grid-cols-2 mb-3">
        <div>Date of data</div>
        <div class="text-right">{last_Counter_Info}</div>       
        <div>Import counter</div>
        <div class="text-right">{typeof last_Counter_Value !== 'undefined' ? last_Counter_Value.toFixed(0) : '-'} kWh</div>
        <div>Consumption</div>
        <div class="text-right">{typeof last_Counter_Diff !== 'undefined' ? last_Counter_Diff.toFixed(0) : '-'} kWh</div>
        <div>Costs</div>
        <div class="text-right">{typeof last_Counter_Costs !== 'undefined' ? last_Counter_Costs.toFixed(2) : '-'} EUR</div>
    </div>
    {#if hasExport}
        <strong>Export</strong>
        <div class="grid grid-cols-2 mb-3">
            <div>Date of data</div>
            <div class="text-right">{last_expCounter_Info}</div>       
            <div>Export counter</div>
            <div class="text-right">{typeof last_expCounter_Value !== 'undefined' ? last_expCounter_Value.toFixed(0) : '-'} kWh</div>
            <div>Export</div>
            <div class="text-right">{typeof last_expCounter_Diff !== 'undefined' ? last_expCounter_Diff.toFixed(0) : '-'} kWh</div>
            <div>Refund</div>
            <div class="text-right">{typeof last_expCounter_Costs !== 'undefined' ? last_expCounter_Costs.toFixed(2) : '-'} EUR</div>
        </div>
        {/if}
</div>
