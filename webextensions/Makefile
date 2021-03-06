NPM_MOD_DIR := $(CURDIR)/node_modules
NPM_BIN_DIR := $(NPM_MOD_DIR)/.bin

PACKAGE_NAME = rescue-conflicting-alternatives

.PHONY: xpi install_dependency install_hook lint format update_extlib install_extlib test

all: xpi

install_dependency:
	[ -e "$(NPM_BIN_DIR)/eslint" -a -e "$(NPM_BIN_DIR)/jsonlint-cli" ] || npm install

install_hook:
	echo '#!/bin/sh\nmake lint' > "$(CURDIR)/../.git/hooks/pre-commit" && chmod +x "$(CURDIR)/../.git/hooks/pre-commit"

lint: install_dependency
	"$(NPM_BIN_DIR)/eslint" . --ext=.js --report-unused-disable-directives
	find . -type d -name node_modules -prune -o -type f -name '*.json' -print | xargs "$(NPM_BIN_DIR)/jsonlint-cli"

format: install_dependency
	"$(NPM_BIN_DIR)/eslint" . --ext=.js --report-unused-disable-directives --fix

xpi: update_extlib install_extlib lint
	rm -f ./*.xpi
	zip -r -9 $(PACKAGE_NAME)-we.xpi manifest.json common background dialog options resources extlib _locales -x '*/.*' >/dev/null 2>/dev/null

update_extlib:
	git submodule update --init

install_extlib:
	rm -f extlib/*.js extlib/*.css
	cp ../submodules/webextensions-lib-configs/Configs.js extlib/; echo 'export default Configs;' >> extlib/Configs.js
	cp ../submodules/webextensions-lib-options/Options.js extlib/; echo 'export default Options;' >> extlib/Options.js
	cp ../submodules/webextensions-lib-l10n/l10n.js extlib/; echo 'export default l10n;' >> extlib/l10n.js
	#cp ../submodules/webextensions-lib-dialog/dialog.js extlib/
	cp ../submodules/webextensions-lib-dialog/dialog.css extlib/
	cp ../submodules/webextensions-lib-message-body/messageBody.js extlib/

test:
	npm run test
