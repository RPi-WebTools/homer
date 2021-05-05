<template>
    <div>
        <div class="grid-container full-height">
            <div class="grid-item-top-1 box top-level">
                <div class="level">
                    <div class="level-left">
                        <h3 class="title is-3 level-item">Backlog</h3>
                    </div>
                    <div class="level-right">
                        <button class="button level-item" id="add-btn" @click="openModal()">
                            <span class="icon is-small">
                                <i class="fas fa-plus"></i>
                            </span>
                        </button>
                    </div>
                </div>
            </div>
            <div class="grid-item-top-2 box top-level">
                <div class="level">
                    <div class="level-left">
                        <h3 class="title is-3 level-item">Done</h3>
                    </div>
                    <div class="level-right">
                        <button class="button is-rounded level-item" disabled id="invisible-button">
                        </button>
                    </div>
                </div>
            </div>
            <div class="grid-item-inner-1 box top-level" ref="col_unsure">
                <h4 class="title is-4">Unsure</h4>
            </div>
            <div class="grid-item-inner-2 box top-level" ref="col_todo">
                <h4 class="title is-4">To Do</h4>
            </div>
            <div class="grid-item-inner-3 box top-level" ref="col_ready">
                <h4 class="title is-4">Is Ready</h4>
            </div>
            <div class="grid-item-inner-4 box top-level" ref="col_done">
                <h4 class="title is-4"><br></h4>
            </div>
            <div/>
        </div>
        <div class="modal" id="add-modal">
            <div class="modal-background"></div>
            <div class="modal-card">
                <header class="modal-card-head">
                    <p class="modal-card-title">Add Card</p>
                    <button class="delete" aria-label="close" @click="closeModal()"></button>
                </header>
                <section class="modal-card-body">
                    <div class="field">
                        <label class="label">Title</label>
                        <div class="control">
                            <input class="input" id="inputTitle" type="text" placeholder="Title" v-model="modalTitleInput" required>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Subtitle</label>
                        <div class="control">
                            <input class="input" id="inputSubtitle" type="text" placeholder="e.g. 2021 or S01" v-model="modalSubtitleInput" required>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">IMDb ID</label>
                        <div class="control">
                            <input class="input" type="text" placeholder="e.g. tt7888964" v-model="modalImdbInput">
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Wanted Audio Language</label>
                        <div class="control">
                            <div class="select">
                                <select v-model="modalLangSelect">
                                    <option>🇬🇧</option>
                                    <option>🇩🇪</option>
                                </select>
                            </div>
                        </div>
                    </div>
                    <div class="field">
                        <label class="label">Target Column</label>
                        <div class="control">
                            <div class="select">
                                <select v-model="modalColSelect">
                                    <option value="unsure">Backlog - Unsure</option>
                                    <option value="todo">Backlog - To Do</option>
                                    <option value="ready">Backlog - Is Ready</option>
                                    <option value="done">Done</option>
                                </select>
                            </div>
                        </div>
                    </div>
                </section>
                <footer class="modal-card-foot">
                    <button type="submit" class="button is-success" @click="createCard()">Confirm</button>
                    <button class="button" @click="closeModal()">Cancel</button>
                </footer>
            </div>
        </div>
        <CardItem class="is-invisible" />
    </div>
</template>

<script>
import Vue from 'vue'
import CardItem from "./CardItem.vue"

export default {
    name: "Kanban",
    components: {
        CardItem
    },
    data() {
        return {
            modalTitleInput: '',
            modalSubtitleInput: '',
            modalImdbInput: '',
            modalLangSelect: '🇬🇧',
            modalColSelect: 'todo'
        };
    },
    methods: {
        openModal() {
            document.getElementById("add-modal").classList.add("is-active");
        },
        closeModal() {
            document.getElementById("add-modal").classList.remove("is-active");
        },
        getCurrentCol(name) {
            let col;
            switch (name) {
                case "unsure":
                    col = this.$refs.col_unsure;
                    break;
                case "todo":
                    col = this.$refs.col_todo;
                    break;
                case "ready":
                    col = this.$refs.col_ready;
                    break;
                case "done":
                    col = this.$refs.col_done;
                    break;
                default:
                    col = null;
                    break;
            }
            return col;
        },
        getNextCol(cur, dir) {
            let col;
            switch (cur) {
                case "unsure":
                    dir == 0 ? col = this.$refs.col_unsure : col = this.$refs.col_todo;
                    break;
                case "todo":
                    dir == 0 ? col = this.$refs.col_unsure : col = this.$refs.col_ready;
                    break;
                case "ready":
                    dir == 0 ? col = this.$refs.col_todo : col = this.$refs.col_done;
                    break;
                case "done":
                    dir == 0 ? col = this.$refs.col_ready : col = this.$refs.col_done;
                    break;
                default:
                    col = null;
                    break;
            }
            return col;
        },
        createCard() {
            this.modalTitleInput == '' ? document.getElementById('inputTitle').classList.add('is-danger') :
                                         document.getElementById('inputTitle').classList.remove('is-danger');
            this.modalSubtitleInput == '' ? document.getElementById('inputSubtitle').classList.add('is-danger') :
                                            document.getElementById('inputSubtitle').classList.remove('is-danger');
            if (this.modalColSelect == '' || this.modalTitleInput == '' || this.modalSubtitleInput == '') {
                return;
            }

            let CompCls = Vue.extend(CardItem);
            let cardInstance = new CompCls({
                propsData: {
                    lang: this.modalLangSelect,
                    title: this.modalTitleInput,
                    subtitle: this.modalSubtitleInput,
                    imdbId: this.modalImdbInput,
                    col: this.modalColSelect
                }
            });
            cardInstance.$mount();
            cardInstance.$el.classList.add('mb-3');

            cardInstance.$on('move-left', (title, col) => {
                console.log(title + ' left from ' + col);
                let c = this.getNextCol(col, 0);
                c.appendChild(cardInstance.$el);
            });
            cardInstance.$on('move-right', (title, col) => {
                console.log(title + ' right from ' + col);
                let c = this.getNextCol(col, 1);
                c.appendChild(cardInstance.$el);
            });
            cardInstance.$on('destroy-card', col => {
                console.log('destroy in ' + col);
                let c = this.getCurrentCol(col);
                c.removeChild(cardInstance.$el);
                cardInstance.$destroy();
            });

            let col = this.getCurrentCol(this.modalColSelect);
            if (col != null) {
                col.appendChild(cardInstance.$el);
                this.closeModal();
                this.modalTitleInput = '';
                this.modalSubtitleInput = '';
                this.modalImdbInput = '';
            }
        }
    }
}
</script>

<style lang="scss" scoped>
.box.top-level {
    background-color: var(--highlight-primary);
}
.box.inner-level {
    background-color: var(--card-background);
}

.full-height {
    height: 100%;
}

#add-btn {
    background-color: var(--highlight-secondary);
    border: 1px solid var(--card-background);
}

#invisible-button {
    color: var(--highlight-primary);
    background-color: var(--highlight-primary);
    border: none;
    cursor: default;
}

.grid-container {
    display: grid;
    grid-column-gap: 20px;
    grid-template-columns: 25% 25% 25% 25%;
    grid-template-areas: "header1 header1 header1 header2"
                         " main1 main2 main3 main4";
}
.grid-item-top-1 {
    grid-area: header1;
}
.grid-item-top-2 {
    grid-area: header2;
}
.grid-item-inner-1 {
    grid-area: main1;
}
.grid-item-inner-2 {
    grid-area: main2;
}
.grid-item-inner-3 {
    grid-area: main3;
}
.grid-item-inner-4 {
    grid-area: main4;
}
</style>