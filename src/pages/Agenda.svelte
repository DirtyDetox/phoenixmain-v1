<script>
    import SvelteMarkdown from 'svelte-markdown'

    import PageHeader from '../components/PageHeader.svelte';

    const API_URL = __myapp.env.API_URL
	const APP_HOST = __myapp.env.APP_HOST
	const APP_PROTOCOL = __myapp.env.APP_PROTOCOL
    console.log(__myapp.env)
    
    const day_names = ["Søndag", "Mandag", "Tirsdag", "Onsdag", "Torsdag", "Fredag", "Lørdag"]
    
    const fetchAgenda = (async () => {
        const agenda = await (await fetch(`${API_URL}/agenda`)).json()
        const agenda_days = []

        agenda.forEach(entry => {
            const date = new Date(entry.time*1000);
            console.log(date)
            const entry_day = date.getDay()
            console.log(entry_day)

            let dayEntry = agenda_days.find(day => {
                return day.dayNo === entry_day
            })
            if(!dayEntry) {
                dayEntry = {
                    dayNo: entry_day,
                    name: day_names[entry_day],
                    sort_key: entry.time,
                    dateStr: `${date.toLocaleString('default', { day: "numeric", month: 'short' })}`,
                    agenda: []
                }
                agenda_days.push(dayEntry)
            }
            dayEntry.agenda.push(entry)
        })
        console.log(agenda_days)
        return agenda_days
    })()
</script>

<div class="contentSimulator">
    <PageHeader><h1>Agenda</h1></PageHeader>
    <div class="content">
        {#await fetchAgenda}
        <i>Henter agenda</i>
        {:then data}
        <div class="calendarContainer">
            <div class="day">
                <div class="dayTitle">Hele arrangementet</div>
                <div class="dayEntry">
                    <p class="entryTitle">Minecraft build battle</p>
                    <p class="entryDesc"><code>mc.phoenixlan.no</code></p>
                    <p class="entryTime">Fra lørdag 13:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">Phoenix datasnok</p>
                    <p class="entryDesc">Tech-konkurranse: <code>datasnok.phoenixlan.no</code></p>
                    <p class="entryTime">Fra lørdag 15:00</p>
                </div>
            </div>
            {#each data as day}
            <div class="day">
                <div class="dayTitle">
                    <div class="dayTitleInner">
                        {day.name}<i class="dayTitleDate">{day.dateStr}</i>
                    </div>
                </div>
                {#each day.agenda as entry}
                <div class="dayEntry">
                    <p class="entryTitle">{ entry.title }</p>
                    {#if entry.description}
                    <p class="entryDesc">{ entry.description}</p>
                    {/if}
                    <p class="entryTime">{ new Date(entry.time*1000).toLocaleTimeString() }</p>
                </div>
                {/each}
            </div>
            {/each}
            <!--
            <div class="day">
                <div class="dayTitle">Fredag</div>
                <div class="dayEntry">
                    <p class="entryTitle">Kahoot</p>
                    <p class="entryTime">18:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">Skribble</p>
                    <p class="entryTime">19:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">Minecraft: Hunger games</p>
                    <p class="entryTime">20:30 - 0:30</p>
                </div>
            </div>
            <div class="day">
                <div class="dayTitle">Lørdag</div>
                <div class="dayEntry">
                    <p class="entryTitle">Rocket League 2v2</p>
                    <p class="entryTime">13:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">Kahoot</p>
                    <p class="entryTime">17:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">CS:GO</p>
                    <p class="entryTime">18:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">Paint-compo</p>
                    <p class="entryTime">23:00</p>
                </div>
            </div>
            <div class="day">
                <div class="dayTitle">Søndag</div>
                <div class="dayEntry">
                    <p class="entryTitle">Paint-compo</p>
                    <p class="entryTime">01:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">Jackbox</p>
                    <p class="entryTime">02:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">League of Legends 1v1</p>
                    <p class="entryTime">15:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">Jackbox</p>
                    <p class="entryTime">17:00</p>
                </div>
                <div class="dayEntry">
                    <p class="entryTitle">Premieutdeling</p>
                    <p class="entryDesc">Build battle og datasnok</p>
                    <p class="entryTime">17:45</p>
                </div>
            </div>
-->
        </div>
        {:catch error}
        <b>Kunne ikke hente data: {error}</b>
        {/await}
    </div>
</div>

<style>

.calendarContainer {
    width: 100%;
    display: flex;
    justify-content: center;
    flex-wrap: wrap;
}

.day {
    margin: 2em;
    width: 20em;
}

.dayTitle {
    width: 100%;
    background-color: white;
    color: #333;

    font-size: 1.5em;
    padding: 0.4em;
}
.dayTitleDate {
    margin-left: 0.2em;
    font-size: 0.7em;
}

.dayEntry {
    text-align: left;
    padding: 1em;
}

.entryTitle {
    margin: 0;
    font-size:2em;
}

.entryTime {
    margin: 0.6em 0 0.6em 0;
    font-size:1.1em;
}

.contentSimulator {

}


.content {
    background-color: #111111;
    width: 100%;
}
</style>
