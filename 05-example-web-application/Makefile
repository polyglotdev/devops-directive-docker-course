DATABASE_URL:=postgres://postgres:foobarbaz@localhost:5435/postgres

.PHONY: postgres
postgres: ## Start postgres container
	@echo Starting postgres container on port 5435
	-docker run \

		--name postgres-dk-course \
		-e POSTGRES_PASSWORD=foobarbaz \
		-v pgdata:/var/lib/postgresql/data \
		-d \
		-p 5435:5432 \
		postgres:15.1-alpine

.PHONY: node
node: ## Start node api
	@echo Starting node api
	cd api-node && \
		DATABASE_URL=${DATABASE_URL} \
		npm run dev

.PHONY: golang
golang: ## Start golang api
	@echo Starting golang api
	cd api-golang && \
		DATABASE_URL=${DATABASE_URL} \
		go run main.go

.PHONY: react
react: ## Start react client
	@echo Starting react client
	cd client-react && \
		npm run dev

.PHONY: run-all
run-all: ## Start all services
	@echo Starting all services
	make postgres
	make node
	make react

.PHONY: stop-all
stop-all: ## Stop all services
	@echo Stopping all services
	make stop-postgres
	make stop-node
	make stop-react

.PHONY: run-golang-api-container
run-golang-api-container: ## Build golang container
	@echo Building golang container
	cd api-golang && \
		DATABASE_URL=${DATABASE_URL} \
		docker build -t api-golang . && \
		docker run -d --name api-golang api-golang

.PHONY: run-react-container
run-react-container: ## Build react container
	@echo Building react container
	cd client-react && \
		docker build -t client-react . && \
		docker run -d --name client-react client-react

.PHONY: run-node-api-container
run-node-api-container: ## Build node container
	@echo Building node container
	cd api-node && \
		DATABASE_URL=${DATABASE_URL} \
		docker build -t api-node . && \
		docker run -d --name api-node api-node

.PHONY: run-all-containers
run-all-containers: ## Run all containers
	@echo Running all containers
	make run-golang-api-container
	make run-react-container
	make run-node-api-container

.PHONY: stop-all-containers
stop-all-containers: ## Stop all containers
	@echo Stopping all containers
	docker stop api-golang
	docker stop client-react
	docker stop api-node

.PHONY: help
help: ## Display details for all commands
	@awk 'BEGIN {FS = ":.*?##"; printf "\nUsage:\n  make \033[36m<target>\033[0m\n"} /^[a-zA-Z0-9_-]+:.*?##/ { printf "  \033[36m%-25s\033[0m %s\n", $$1, $$2 } /^##@/ { printf "\n\033[1m%s\033[0m\n", substr($$0, 5) } ' $(MAKEFILE_LIST)
