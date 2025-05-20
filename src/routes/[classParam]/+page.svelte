<script lang="ts">
    import { onMount } from 'svelte';
    import { page } from '$app/state';

    let schoolBreaks: any[] = $state([])

    let dateString: string = $state("");

    let timeString: string = $state("");

    let classParam = page.params.classParam

    let IsSchoolTime: boolean = $state(false);


    
    function checkBreakTime(time: Date, breakSchedule: Array<any>) {
        const now = time
        const currentHour = now.getHours();
        const currentMinute = now.getMinutes();

        const currentTimeInMinutes = currentHour * 60 + currentMinute;

        for (const breakPeriod of breakSchedule) {
            const fromParts = breakPeriod.from.split(':').map(Number);
            const toParts = breakPeriod.to.split(':').map(Number);

            const fromTimeInMinutes = fromParts[0] * 60 + fromParts[1];
            const toTimeInMinutes = toParts[0] * 60 + toParts[1];

            if (currentTimeInMinutes >= fromTimeInMinutes && currentTimeInMinutes < toTimeInMinutes) {
                return true; // It's break time!
            }
        }

        return false; // Not break time right now
    }

    function getWeekNumber(date: any) {
        // Copy the date to avoid modifying the original object
        date = new Date(Date.UTC(date.getFullYear(), date.getMonth(), date.getDate()));
        // Set to nearest Thursday: current date + 4 - (date.getDay() || 7)
        date.setUTCDate(date.getUTCDate() + 4 - (date.getUTCDay()||7));
        // Get the first day of year
        const yearStart: Date = new Date(Date.UTC(date.getUTCFullYear(),0,1));
        // Calculate full weeks to nearest Thursday
        const diff = (date - Number(yearStart));
        const oneDay = 1000 * 60 * 60 * 24;
        const dayOfYear = Math.floor(diff / oneDay);
        const week = Math.ceil((dayOfYear + yearStart.getUTCDay() + 1) / 7);
        return week;
    }

    function updateTime() {
        const now = new Date();

        let hours: any = now.getHours();
        let minutes: any = now.getMinutes();
        let seconds: any = now.getSeconds();
        
        let date: any = now.getDate();

        let day: string = ["Sunday", "Monday", "Tuesday", "Wednesday", "Thursday", "Friday", "Saturday"][now.getDay()];
        let month: string = ['January', 'February', 'March', 'April', 'May', 'June', 'July', 'August', 'September', 'October', 'November', 'December'][now.getMonth()];

        // Add leading zeros if needed
        hours = (hours < 10) ? "0" + hours : hours;
        minutes = (minutes < 10) ? "0" + minutes : minutes;
        seconds = (seconds < 10) ? "0" + seconds : seconds;

        date = (date < 10) ? "0" + date : date;

        IsSchoolTime = !checkBreakTime(now, schoolBreaks)

        timeString = `${hours}:${minutes}:${seconds}`;
        dateString = `${day}, ${date} ${month}  , ${now.getFullYear()}, week: ${getWeekNumber(now)}`;
    }
    
    onMount(async ()=>{
        
        fetch("http://ubuntu-2210-oliver:1880/schedule")
        .then((response) => response.json())
        .then((data) => {
            schoolBreaks = data
        })
        setInterval(()=>{
            updateTime()
        }, 5)
        setTimeout(()=>{location.reload()},1000*60*24)
    })

</script>
<div class="w-screen h-screen bg-stone-900 text-slate-300 flex flex-col">
    <div class="basis-7/8 flex flex-col justify-center items-center w-full">
            <span>Klasse: {classParam}!! Velkommen!!!</span>
        <div class="w-fit h-fit">
            <h1 class="font-bold text-[10rem] leading-1rem text-center w-fit">
                {timeString}
            </h1>
            <h2 class="font-light text-2xl float-right text-right w-fit">
                {dateString}
            </h2>
        </div>
    </div>
    <div class={`basis-1/8 ${!IsSchoolTime ? 'bg-green-600' : 'bg-red-600'} flex flex-col content-center justify-center`}>
        <h1 class="text-8xl h-fit text-center">
            {#if IsSchoolTime}
                Work
            {:else}
                Break
            {/if}
        </h1>
    </div>
</div>
