<script lang="ts">
    import { onDestroy, onMount } from 'svelte';

    export let untilDate: Date | null = null;

    let days, hours, minutes, seconds;

    $: lastMinute = (days === 0) && (hours === 0) && (minutes === 0);

    export function isLastMinute() {
        return lastMinute;
    }

    function refresh() {
        if (!untilDate) {
            return;
        }
        var delta = Math.abs(untilDate.valueOf() - new Date().valueOf()) / 1000;
        days = Math.floor(delta / 86400);
        delta -= days * 86400;
        hours = Math.floor(delta / 3600) % 24;
        delta -= hours * 3600;
        minutes = Math.floor(delta / 60) % 60;
        delta -= minutes * 60;
        seconds = delta % 60;
    }

    let interval: ReturnType<typeof setInterval> | undefined;

    onMount(async () => {
        refresh();
        interval = setInterval(refresh, 1000);
    });

    onDestroy(() => {
        if (interval) {
            clearInterval(interval);
            interval = undefined;
        }
    });
</script>

<div class:blink={lastMinute} class="flex justify-center items-center gap-5">
    {#if !lastMinute}
        <div>
            <span class="countdown font-mono text-3xl">
                <span style="--value:{days};"></span>
            </span>
            days
        </div> 
        <div>
            <span class="countdown font-mono text-3xl">
                <span style="--value:{hours};"></span>
            </span>
            hours
        </div> 
        <div>
            <span class="countdown font-mono text-3xl">
                <span style="--value:{minutes};"></span>
            </span>
            min
        </div>
    {/if}
    <div>
        <span class="countdown font-mono text-3xl" class:text-5xl={lastMinute}>
            <span style="--value:{seconds};"></span>
        </span>
        sec
    </div>
</div>