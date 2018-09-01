<template>
	<div class="wrapper">
		<header class="filters">
			<div class="formSection">
				<label for="sorting" class="formSection__label">Sort By:</label>
				<select name="sorting" id="sorting" @change="sortOption(sort)" v-model="sort">
					<option value="clearSort" selected>Select sorting</option>
					<option value="ByNameASC">alphabetical: a-z</option>
					<option value="ByNameDESC">alphabetical: z-a</option>
					<option value="ByPriceASC">price: highest first</option>
					<option value="ByPriceDESC">price: lowest first</option>
				</select>
			</div>

			<div class="formSection">
				<span class="formSection__label">Filter:</span>
				<div class="formGroup">
					<input class="formGroup__check" type="checkbox" id="sale" v-model="saleFilter">
					<label class="formGroup__label" for="sale">Sale</label>
				</div>

				<div class="formGroup">
					<input class="formGroup__check" type="checkbox" id="availability" v-model="availabilityFilter">
					<label class="formGroup__label" for="availability">Availability</label>
				</div>
			</div>
		</header>


		<div class="cards">
			<template v-for="card in sortedCards">
				<card
						:card="card"
						:isInCart="isInCart(card.name)"
						:isInFavorites="isInFavorites(card.name)"
						@addToCard="addToCard($event)"
						@addToFavorites="addToFavorites($event)"
				></card>
			</template>
		</div>

		<div class="pagination">
			<button @click="back()" class="btn" :disabled="currentPage === 1">Back</button>
			<template v-for="i in Math.ceil((cards.length)/rowsPerPage)">
				<i :class="{'active': i === currentPage}" @click="currentPage = i">{{ i }}</i>
			</template>
			<button @click="next()" class="btn" :disabled="currentPage === Math.ceil((cards.length)/rowsPerPage)">Next</button>
		</div>
	</div>

</template>

<script>
    import json from '../json/cards.json'
    import Card from './Card.vue'

    export default {
        name: 'Cards',
        components: {
            Card
        },
        data () {
            return {
                cardsJson: json,
                cards: [],
                cart: [],
                favorites: [],
                rowsPerPage: 4,
                currentPage: 1,
                saleFilter: false,
                availabilityFilter: false,
                sort: 'clearSort'
            }
        },
        watch: {
            saleFilter (val) {
                this.filterBySale(val)
            },
            availabilityFilter (val) {
                this.filterByAvailability(val)
            }
        },
        created () {
            this.cards = this.cardsJson.slice(0)
            this.cart = localStorage.getItem('cart') ? (localStorage.getItem('cart')).split(',') : []
            this.favorites = localStorage.getItem('favorites') ? (localStorage.getItem('favorites')).split(',') : []

            if (localStorage.getItem('sort')) {
                this.sort = localStorage.getItem('sort')
                localStorage.getItem('sort') === 'ByNameASC' ? this.sortByNameASC() : null
                localStorage.getItem('sort') === 'ByNameDESC' ? this.sortByNameDESC() : null
                localStorage.getItem('sort') === 'ByPriceASC' ? this.sortByPriceASC() : null
                localStorage.getItem('sort') === 'ByPriceDESC' ? this.sortByPriceDESC() : null
            }

            if (localStorage.getItem('filterBySale') && localStorage.getItem('filterBySale') === 'true') {
                this.saleFilter = true
            }

            if (localStorage.getItem('filterByAvailability') && localStorage.getItem('filterByAvailability') === 'true') {
                this.availabilityFilter = true
            }
        },
        computed: {
            sortedCards () {
                const lastPage = Math.ceil((this.cards.length) / this.rowsPerPage)
                this.currentPage = this.currentPage > lastPage ? lastPage : this.currentPage
                return this.cards.filter((row, index) => {
                    let start = (this.currentPage - 1) * this.rowsPerPage
                    let end = this.currentPage * this.rowsPerPage
                    if (index >= start && index < end) return true
                })
            }
        },
        methods: {
            sortOption (sort) {
                switch (sort) {
                    case 'ByNameASC': {
                        this.sortByNameASC()
                        break
                    }
                    case 'ByNameDESC': {
                        this.sortByNameDESC()
                        break
                    }
                    case 'ByPriceASC': {
                        this.sortByPriceASC()
                        break
                    }
                    case 'ByPriceDESC': {
                        this.sortByPriceDESC()
                        break
                    }
                    case 'clearSort': {
                        this.clearSort()
                    }
                }
            },
            addToCard (name) {
                if (!this.cart.includes(name)) {
                    this.cart.push(name)
                    localStorage.setItem('cart', this.cart)
                }
            },
            addToFavorites (name) {
                if (!this.favorites.includes(name)) {
                    this.favorites.push(name)
                    localStorage.setItem('favorites', this.favorites)
                } else {
                    const index = this.favorites.indexOf(name);
                    if (index !== -1) this.favorites.splice(index, 1);
                    localStorage.setItem('favorites', this.favorites)
				}
            },
            sortByNameASC () {
                this.cards.sort((a, b) => a.name.toLowerCase() > b.name.toLowerCase() ? 1
                    : (a.name.toLowerCase() < b.name.toLowerCase() ? -1 : 0))
                localStorage.setItem('sort', 'ByNameASC')
            },
            sortByNameDESC () {
                this.cards.sort((a, b) => a.name.toLowerCase() < b.name.toLowerCase() ? 1
                    : (a.name.toLowerCase() > b.name.toLowerCase() ? -1 : 0))
                localStorage.setItem('sort', 'ByNameDESC')
            },
            sortByPriceASC () {
                this.cards.sort((a, b) => {
						if (!a.current_price) return 1
						if (!b.current_price) return -1
                        return a.current_price < b.current_price ? 1 : (a.current_price > b.current_price ? -1 : 0)
                    }
                )
                localStorage.setItem('sort', 'ByPriceASC')
            },
            sortByPriceDESC () {
                this.cards.sort((a, b) => {
                        if (!a.current_price) return 1
                        if (!b.current_price) return -1
                        return a.current_price > b.current_price ? 1 : (a.current_price < b.current_price ? -1 : 0)
                    }
                )
                localStorage.setItem('sort', 'ByPriceDESC')
            },

            filterBySale (isSaleFlag) {
                localStorage.setItem('filterBySale', isSaleFlag)
                this.filter(isSaleFlag, 'old_price')
				(!isSaleFlag && this.availabilityFilter) ? this.filterByAvailability(this.availabilityFilter) : null
            },
            filterByAvailability (isAvailableFlag) {
                localStorage.setItem('filterByAvailability', isAvailableFlag)
                this.filter(isAvailableFlag, 'current_price')
				(!isAvailableFlag && this.saleFilter) ? this.filterBySale(this.saleFilter) : null
            },
			filter (flag, filterRule) {
                if (flag) {
                    this.cards = this.cards.filter((val) => {
                        return val[filterRule]
                    })
                } else {
                    this.cards = this.cards.concat(this.cardsJson.filter((val) => {
                        return !val[filterRule]
                    }))
                }
			},
            clearSort () {
                this.cards = this.cardsJson.slice(0)
            },
            isInCart (name) {
                return !this.cart.includes(name)
            },
            isInFavorites (name) {
                return this.favorites.includes(name)
			},
            next () {
                if ((this.currentPage * this.rowsPerPage) < this.cards.length) {
                    this.currentPage++
                }
            },
            back () {
                if (this.currentPage > 1) {
                    this.currentPage--
                }
            }
        }
    }
</script>

<style scoped lang="scss">
	@import '../scss/common/helpers';

	.filters {
		.formGroup {
			&:not(:last-child) {
				margin-right: 14px;
			}
		}
	}

	.cards {
		display: flex;
		flex-wrap: wrap;
		margin-top: 20px;
		justify-content: center;
		margin-left: -15px;
		margin-right: -15px;

		@include for-desktop-up {
			justify-content: flex-start;
		}
	}

	.pagination {
		display: flex;
		justify-content: center;
		align-items: center;
		margin-top: 60px;
		text-align: center;

		i {
			margin: 0 8px;
			cursor: pointer;

			&.active {
				color: #a1a1a1;
			}
		}
	}
</style>
