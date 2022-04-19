build-LambdaFunctionOverHttps:
		cp *.js "$(ARTIFACTS_DIR)"
		cp package.json "$(ARTIFACTS_DIR)"
		cp package-lock.json "$(ARTIFACTS_DIR)"
		cd $(ARTIFACTS_DIR) && npm install --production --arch=x64 --platform=linux
