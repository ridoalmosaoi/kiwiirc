<template>
    <div class="kiwi-mediaviewer">
        <div class="kiwi-mediaviewer-controls">
            <a
                class="u-button u-button-warning kiwi-mediaviewer-controls-close"
                @click="closeViewer"
            >
                <i class="fa fa-window-close" aria-hidden="true"></i>
            </a>
        </div>
        <div :key="url">
            <a
                v-bind:href="url"
                class="embedly-card"
                :data-card-key="embedlyKey"
                data-card-chrome="0"
                data-card-controls="0"
                data-card-recommend="0"
            >{{$t('media_loading', {url: url})}}</a>
        </div>
    </div>
</template>

<script>

import state from 'src/libs/state';

let embedlyTagIncluded = false;

export default {
    data: function data() {
        return {
        };
    },
    props: ['url'],
    computed: {
        embedlyKey: function embedlyKey() {
            return state.settings.embedly.key;
        },
    },
    methods: {
        updateEmbed: function updateEmbed() {
            let checkEmbedlyAndShowCard = () => {
                // If the embedly function doesn't exist it's probably still loading
                // the embedly script
                if (typeof window.embedly !== 'function') {
                    setTimeout(checkEmbedlyAndShowCard, 100);
                    return;
                }

                window.embedly('card', { selector: '.embedly-card' });
            };

            if (!embedlyTagIncluded) {
                let head = document.getElementsByTagName('head')[0];
                let script = document.createElement('script');
                script.type = 'text/javascript';
                script.src = '//cdn.embedly.com/widgets/platform.js';
                head.appendChild(script);
                embedlyTagIncluded = true;
            }

            checkEmbedlyAndShowCard();
        },
        closeViewer: function closeViewer() {
            state.$emit('mediaviewer.hide');
        },
    },
    created: function created() {
        this.updateEmbed();
    },
    watch: {
        url: function watchUrl() {
            this.updateEmbed();
        },
    },
};

</script>

<style>
.kiwi-mediaviewer {
    box-sizing: border-box;
}
</style>
