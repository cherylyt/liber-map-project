<template>
    <q-list>
        <template v-for="item in items" :key="item.sha">
            <q-expansion-item v-if="item.type !== 'file'" :label="item.name">
                <template v-slot:default>
                    <div style="padding-left: 21.6px">
                        <q-list style="border-left: 1px solid rgba(0, 0, 0, .12)">
                            <nested-structure :items="item.children" />
                        </q-list>
                    </div>
                </template>
            </q-expansion-item>

            <q-item v-else clickable v-ripple @click="console.log(item)">
                <q-item-section>{{ cleanPath(item.name) }}</q-item-section>
            </q-item>
        </template>
    </q-list>
</template>

<script>
export default {
    name: 'NestedStructure',
    props: {
        items: {
            type: Array,
            required: true,
            default: () => []
        }
    },
    methods: {
        cleanPath(path) {
            // Add your existing cleanPath implementation here
            return path.replace(/[[\]]/g, '').replace(/\.kml$/, ''); // Remove [,], and .kml
        }
    }
}
</script>