<template>
    <div class="card">
        <!--<div class="card-image">
            <figure class="image is4by3">
                <img src="https://bulma.io/images/placeholders/1280x960.png" alt="Placeholder">
            </figure>
        </div>-->
        <div class="card-content is-clickable" @click="onClick()">
            <div class="media">
                <div class="media-left">
                    <p v-if="lang == '🇩🇪'">🇩🇪</p>
                    <p v-else>🇬🇧</p>
                    <span v-if="type == 'tv'" class="icon is-small subtitle">
                        <i class="fas fa-tv"></i>
                    </span>
                    <span v-else-if="type == 'movie'" class="icon is-small subtitle">
                        <i class="fas fa-film"></i>
                    </span>
                </div>
                <div class="media-content">
                    <p class="title is-4">{{ title }}</p>
                    <p class="subtitle is-6">{{ subtitle }}</p>
                </div>
            </div>
        </div>
        <footer class="card-footer">
            <p class="card-footer-item is-paddingless">
                <button class="button footer-btn is-fullwidth" @click="onClickLeft()">◀️</button>
            </p>
            <p class="card-footer-item is-paddingless">
                <button class="button footer-btn is-fullwidth" @click="remove()">❌</button>
            </p>
            <p class="card-footer-item is-paddingless">
                <button class="button footer-btn is-fullwidth" @click="onClickRight()">▶️</button>
            </p>
        </footer>
    </div>
</template>

<script>
export default {
    name: "CardItem",
    props: {
        lang: String,
        type: String,
        title: String,
        subtitle: String,
        imdbId: String,
        col: String
    },
    data() {
        return {
            column: this.col,
        }
    },
    methods: {
        onClick () {
            window.open("https://imdb.com/title/" + this.imdbId, "_blank")
        },
        onClickLeft() {
            this.$emit('move-left', this.title, this.column, this.subtitle);
            this.column = this.getNextCol(this.column, 0);
        },
        onClickRight() {
            this.$emit('move-right', this.title, this.column, this.subtitle);
            this.column = this.getNextCol(this.column, 1);
        },
        remove() {
            this.$emit('destroy-card', this.title, this.column, this.subtitle);
        },
        getNextCol(cur, dir) {
            let col;
            switch (cur) {
                case "unsure":
                    dir == 0 ? col = "unsure" : col = "todo";
                    break;
                case "todo":
                    dir == 0 ? col = "unsure" : col = "ready";
                    break;
                case "ready":
                    dir == 0 ? col = "todo" : col = "done";
                    break;
                case "done":
                    dir == 0 ? col = "ready" : col = "done";
                    break;
                default:
                    col = null;
                    break;
            }
            return col;
        },
    },
}
</script>

<style lang="scss" scoped>
.footer-btn {
    background-color: var(--card-background);
    border: 1px solid var(--card-background);
}
</style>