<template>
    <div class="offline-list shadow-box mb-4">
        <h3>{{ $t("Offline Devices") }}</h3>
        <ul v-if="offlineMonitors.length > 0" class="list-unstyled">
            <li
                v-for="item in offlineMonitors"
                :key="item.monitor.id"
                class="flashing-red mb-2"
            >
                <router-link
                    :to="`/dashboard/${item.monitor.id}`"
                    class="text-danger"
                >
                    {{ item.monitor.name }}
                </router-link>
                <span class="offline-text">— {{ $t("Offline") }} <Datetime :value="item.heartbeat.time" /></span>
            </li>
        </ul>
        <div v-else>
            ✅ {{ $t("All devices online") }}
        </div>
    </div>
</template>

<script>
import Datetime from "./Datetime.vue";

export default {
    name: "OfflineList",
    components: {
        Datetime,
    },
    computed: {
        offlineMonitors() {
            const monitors = Object.values(this.$root.monitorList || {});
            const heartbeats = this.$root.lastHeartbeatList || {};
            return monitors
                .map((m) => ({ monitor: m, heartbeat: heartbeats[m.id] }))
                .filter((item) => item.heartbeat?.status === 0)
                .sort((a, b) =>
                    a.monitor.name.localeCompare(b.monitor.name)
                );
        },
    },
};
</script>

<style scoped>
.flashing-red {
    animation: flash 1.5s infinite;
}

.offline-text {
    font-size: 0.85em;
    font-weight: normal;
    margin-left: 8px;
}

@keyframes flash {
    0%, 100% { opacity: 1; }
    50%       { opacity: 0.4; }
}
</style>
