# publish-to-github

GradleでGitHubリポジトリにパブリッシュする

## 使い方

パブリッシュ先のURLに環境変数の`PUBLISH_PATH`から受け取った値を入れる。

build.gradle.kts
```kotlin
plugins {
    // ...
    `maven-publish`
}

// ...


publishing {
    publications {
        // ...
    }
    val publishPath = System.getenv()["PUBLISH_PATH"]
    if (publishPath != null) {
        repositories {
            maven {
                url = uri(publishPath)
            }
        }
    }
}
```

.github/workflows/publish.yml
```yml
name: Publish
on: [ push ]
concurrency: ci-${{ github.ref }}
jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-java@v3
        with:
          distribution: temurin
          java-version: 17
          cache: gradle
      - name: Publish
        uses: uTen2c/publish-to-github@v1
        with:
          repo: ${{ secrets.REPO }}
          token-user: ${{ secrets.TOKEN_USER }}
          token: ${{ secrets.TOKEN }}
```

# オプション

| name | description | required | default |
| --- | --- | --- | --- |
| repo | パブリッシュ先のリポジトリ。 ex. uten2c/repo | true | |
| token-user | トークンを発行したユーザー。ex. uten2c | true | |
| token | パーソナルアクセストークン | true | |
| publish-path | $PUBLISH_PATHに代入される値 | false | build/repo |
| publish-task | パブリッシュするGradleタスクの名前 | false | publish |
| commit-user-name | コミットユーザー名 | false | ${{ github.actor }} |
| commit-user-email | コミットユーザーメールアドレス | false | ${{ github.actor }}@users.noreply.github.com |
| commit-message | コミットメッセージ | false | update ${{ github.repository }} |
