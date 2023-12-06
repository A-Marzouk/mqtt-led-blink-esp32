<script setup>
import { Head, Link } from '@inertiajs/vue3';
import mqtt from 'mqtt';
import {ref} from "vue";

/* Setup connection and subsribe to the topic */
const url = 'ws://broker.emqx.io:8083/mqtt'

// Create an MQTT client instance
const options = {
    // Clean session
    clean: false,
    connectTimeout: 4000,
    // Authentication
    clientId: 'mqttx_54e258d3',
    username: 'emqx',
    password: 'public',
}

const client  = mqtt.connect(url, options);

client.on('connect', function () {
    console.log('Connected to ESP32.')

    // Subscribe to a topic
    client.subscribe('emqx/esp32', function (err) {
        if (!err) {
            // Publish a message to a topic
            client.publish('emqx/esp32', 'Subscribed to emqx/esp32 topic');
        }
    })
})

// Receive messages
client.on('message', function (topic, message) {
    // message is Buffer
    console.log(message.toString())
})

// Sending messages:
const message = ref("");

const publishMessage = () => {
    client.publish('emqx/esp32', message.value);
    message.value = "";
}

// Toggle LED:
const ledStatus = ref(false);

const turnOnLed = () => {
    if(ledStatus.value === true) return;
    ledStatus.value = true;
    client.publish('emqx/esp32', "led_on");
}

const turnOffLed = () => {
    if(ledStatus.value === false) return;
    ledStatus.value = false;
    client.publish('emqx/esp32', "led_off");
}

</script>

<template>
    <Head title="Welcome" />

    <div
        class="relative sm:flex sm:justify-center sm:items-center min-h-screen bg-dots-darker bg-center bg-gray-100 dark:bg-dots-lighter dark:bg-gray-900 selection:bg-red-500 selection:text-white"
    >
        <div class="max-w-7xl mx-auto p-6 lg:p-8">
            <div class="mt-8">
                <div
                    class="scale-100 p-6 bg-white dark:bg-gray-800/50 dark:bg-gradient-to-bl from-gray-700/50 via-transparent dark:ring-1 dark:ring-inset dark:ring-white/5 rounded-lg shadow-2xl shadow-gray-500/20 dark:shadow-none flex motion-safe:hover:scale-[1.01] transition-all duration-250 focus:outline focus:outline-2 focus:outline-red-500"
                >
                    <div>
                        <h2 class="mt-6 text-xl font-semibold text-gray-900 dark:text-white">MQTT - Basic Example | Blue Falls Manufacturing Ltd.</h2>

                        <p class="mt-4 text-gray-500 dark:text-gray-400 text-sm leading-relaxed">
                            A simple web application using JS websockets for real time connection between browser and ESP32.
                        </p>
                    </div>
                </div>
            </div>

            <div class="mt-8">
                <div class="text-gray-500">
                    Send a text message to ESP32:
                </div>

                <input class="mt-2 mb-6 w-full" type="text" v-model="message">

                <a href="javascript:void(0)" class="mt-4 text-white bg-green-500 px-4 py-2 rounded-md" @click="publishMessage">
                    Publish
                </a>
            </div>

            <hr class="mt-8">

            <div class="mt-8">
                <div class="text-gray-500">
                   Toggle led on pen <b>5</b> to ESP32:
                </div>

                <div class="flex gap-6">
                    <a href="javascript:void(0)" :class="{'opacity-25' : !ledStatus}" class="mt-4 text-white bg-green-500 px-4 py-2 rounded-md" @click="turnOnLed">
                        ON
                    </a>
                    <a href="javascript:void(0)" :class="{'opacity-25' : ledStatus}" class="mt-4 text-white bg-gray-500 px-4 py-2 rounded-md" @click="turnOffLed">
                        OFF
                    </a>
                </div>

            </div>

        </div>
    </div>
</template>

<style>
.bg-dots-darker {
    background-image: url("data:image/svg+xml,%3Csvg width='30' height='30' viewBox='0 0 30 30' fill='none' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M1.22676 0C1.91374 0 2.45351 0.539773 2.45351 1.22676C2.45351 1.91374 1.91374 2.45351 1.22676 2.45351C0.539773 2.45351 0 1.91374 0 1.22676C0 0.539773 0.539773 0 1.22676 0Z' fill='rgba(0,0,0,0.07)'/%3E%3C/svg%3E");
}
@media (prefers-color-scheme: dark) {
    .dark\:bg-dots-lighter {
        background-image: url("data:image/svg+xml,%3Csvg width='30' height='30' viewBox='0 0 30 30' fill='none' xmlns='http://www.w3.org/2000/svg'%3E%3Cpath d='M1.22676 0C1.91374 0 2.45351 0.539773 2.45351 1.22676C2.45351 1.91374 1.91374 2.45351 1.22676 2.45351C0.539773 2.45351 0 1.91374 0 1.22676C0 0.539773 0.539773 0 1.22676 0Z' fill='rgba(255,255,255,0.07)'/%3E%3C/svg%3E");
    }
}
</style>
