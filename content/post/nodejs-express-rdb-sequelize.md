---
title:       "Node.js・ExpressでブラウザからPUT・DELETEリクエストを発行する"
URL:         "nodejs-express-rdb-sequelize"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-08-20
author:      "ぺーぺーSE"
image:       ""
tags:
- node.js
- yarn
- express
- javascript
categories:
- tech
---

Node.js/ExpressアプリケーションからRDBへ接続してみる。  
使用するツール・ライブラリは以下。

- O/R Mapper
    - [Sequelize](http://docs.sequelizejs.com/)
- RDB
    - [SQLite3](https://sqlite.org/index.html)

<!--more-->

以下の記事を読んだ前提で書く。

- [Express入門](https://blog.pepese.com/nodejs-express-basics/)

# 環境設定

先の記事で紹介したプロジェクトにて以下を実行する。

```sh
$ yarn add sequelize sqlite3 morgan
$ mkdir app/db
```

`.gitignore` へ以下を追加。

```
node_modules
app/db/*.sqlite
.DS_Store
./**/.DS_Store
```

# 実装

- `app/repositories/sequelize.js`
    - Sequelizeを使ったRDB接続用モジュール
- `app/repositories/models.js`
    - Clientモデルを作成
    - テーブルの作成とサンプルデータのインサート
- `app/controllers/get_client`
    - 上記の `models.js` 経由でClientの検索


## `app/repositories/sequelize.js`

```javascript
const Sequelize = require('sequelize');
const path = require('path');
const logger = require('morgan');

const sequelize = new Sequelize('SampleDB', '', '', {
  host: 'localhost',
  dialect: 'sqlite',

  pool: {
    max: 5,
    min: 0,
    idle: 10000
  },

  // SQLite only
  storage: path.join(__dirname, '../db/database.sqlite'),

  logging: logger.info // ログをロガーに流す
});

// Test the connection
sequelize
  .authenticate()
  .then(() => {
    console.log('Connection has been established successfully.');
  })
  .catch(err => {
    console.error('Unable to connect to the database:', err);
  });

module.exports = sequelize;
```

## `app/repositories/models.js`

```javascript
const Sequelize = require('sequelize');
const sequelize = require('./sequelize');

const Client = sequelize.define('client', {
  firstName: {
    type: Sequelize.STRING
  },
  lastName: {
    type: Sequelize.STRING
  }
});

// force: true will drop the table if it already exists
Client.sync({force: true}).then(() => {
  // Table created
  return Client.create({
    firstName: 'Hoge',
    lastName: 'Fuge'
  });
});

module.exports = {
  Client: Client
};
```

## `app/controllers/get_clients.js`

```javascript
const models = require('../repositories/models');
const Client = models.Client;

const get_clients = (req, res, next) => {
  Client.findAll().then(clients => {
    res.send(clients);
  });
};

module.exports = get_clients;
```

# 実行

以下で起動。

```sh
$ node app/app.js
```

`http://localhost:3000` にブラウザでアクセスして Client 情報が見れたら成功。

# Association

ちょっとハマったので、ここではモデル同士のリレーション・関連付けについて記載する。  
リレーションには以下がある。

- hasOne
    - 1:1 のリレーション
- belongsTo
    - 1:N のリレーション
    - `hasOne` や `hasMany` される側
- hasMany
    - 1:N のリレーション
- belongsToMany
    - N:M のリレーション

ここでは `belongsToMany` の例のみ記載する。  
[参考/チュートリアル](http://docs.sequelizejs.com/manual/tutorial/associations.html)、[参考/リファレンス](http://docs.sequelizejs.com/class/lib/associations/base.js~Association.html)

## 実装

- app/models/user.js
    - ユーザテーブルのモデル
- app/models/team.js
    - チームテーブルのモデル
- app/models/team-user.js
    - チームテーブルとユーザテーブルの N:M のリレーションを構築する中間テーブルのモデル
- app/repositories/sequelize.js
    - Sequelize のロード・設定を行う
    - Sqlite3 を使用した例になっている
- app/repositories/db.js
    - モデルのリレーション作成、DBとの同期を行う
- app/app.js
    - 適当な実行ファイル、全く参考にならない
    - `node app/app.js` で実行

個人的な好みだが、モデルは **単数形** で作成する。  
また、 **モデルのリレーションを実装してから `sync()` する** のがポイント。（ここでハマった）

### app/models/user.js

```javascript
const Sequelize = require('sequelize');
const sequelize = require('../repositories/sequelize');

const User = sequelize.define('user',
  {
    user_id: {
      type: Sequelize.STRING,
      primaryKey: true,
      allowNull: false,
      autoIncrement: false,
      unique: true
    },
    user_name: {
      type: Sequelize.STRING
    }
  },
  {
    timestamps: false,
    tableName: 'user'
  }
);

module.exports = User;
```

`user_id` が主キー。

### app/models/team.js

```javascript
const Sequelize = require('sequelize');
const sequelize = require('../repositories/sequelize');

const Team = sequelize.define('team',
  {
    team_id: {
      type: Sequelize.STRING,
      primaryKey: true,
      allowNull: false,
      autoIncrement: false,
      unique: true
    },
    team_name: {
      type: Sequelize.STRING
    }
  },
  {
    timestamps: false,
    tableName: 'team'
  }
);

module.exports = Team;
```

`team_id` が主キー。

### app/models/team-user.js

```javascript
const Sequelize = require('sequelize');
const sequelize = require('../repositories/sequelize');

const TeamUser = sequelize.define('team_user',
  {
    team_id: {
      type: Sequelize.STRING,
      primaryKey: true,
      allowNull: false,
      unique: false,
      references: {
        model: "team",
        key: "team_id"
      }
    },
    user_id: {
      type: Sequelize.STRING,
      primaryKey: true,
      allowNull: false,
      unique: false,
      references: {
        model: "user",
        key: "user_id"
      }
    }
  },
  {
    timestamps: false,
    tableName: 'team_user'
  }
);

module.exports = TeamUser;
```

`user_id` 、 `team_id` が複合キーで、それぞれ外部キーを参照しているのがポイント。

### app/repositories/sequelize.js

```javascript
const Sequelize = require('sequelize');
const path = require('path');
const logger = require('morgan');

const sequelize = new Sequelize('SampleDB', '', '', {
  host: 'localhost',
  dialect: 'sqlite',

  pool: {
    max: 5,
    min: 0,
    idle: 10000
  },

  // SQLite only
  storage: path.join(__dirname, '../db/database.sqlite'),

  logging: logger.info // ログをロガーに流す
});

// Test the connection
sequelize
  .authenticate()
  .then(() => {
    console.log('Connection has been established successfully.');
  })
  .catch(err => {
    console.error('Unable to connect to the database:', err);
  });

module.exports = sequelize;
```

### app/repositories/db.js

```javascript
const Sequelize = require('sequelize');
const sequelize = require('./sequelize');
const User = require('../models/user');
const Team = require('../models/team');
const TeamUser = require('../models/team-user');
const UserAccount = require('../models/user-account');

// 以下で m:n のリレーションを作成でき、以下が実行可能になる
// user.addTeam : 既存のteamテーブルのteamモデル（配列も可）をuserと関連付け。
// user.addTeams : 既存のteamテーブルのteamモデル（配列）をuserと関連付け。
// user.countTeams : userに関連付けされたteamを数え上げる。
// user.createTeam : teamオブジェクトを渡して、新規にteamテーブルにteamレコードを作成して且つ関連付け。
// user.getTeams : userと関連付けされたteamモデルを取得する。
// user.hasTeam : userに引数で与えたteamモデルが関連付けされているか確認する。（true/false）
// user.hasTeams : userに引数で与えたteamモデルが関連付けされているか確認する。（true/false）
// user.removeTeam : userと引数で与えたteamモデル（配列も可）の関連付けを削除する。ただし、teamモデルは削除されない。
// user.removeTeams : userと引数で与えたteamモデル（配列）の関連付けを削除する。ただし、teamモデルは削除されない。
// user.setTeams : 意味がわかってない。addのように関連付けはされない。
// 参考：http://docs.sequelizejs.com/class/lib/associations/belongs-to-many.js~BelongsToMany.html
User.belongsToMany(Team, {
  through: TeamUser,
  foreignKey: 'user_id',
  otherKey: 'team_id'
});

// 以下で m:n のリレーションを作成でき、以下が実行可能になる
// team.addUser(s), team.countUsers, team.createUser, team.getUsers,
// team.hasUser(s), team.removeUser(s), team.setUsers
Team.belongsToMany(User, {
  through: TeamUser,
  foreignKey: 'team_id',
  otherKey: 'user_id'
});

User.hasMany(UserAccount, {
  foreignKey: 'user_id'
});
UserAccount.belongsTo(User, {
  foreignKey: 'user_id',
  targetKey: 'user_id'
});

// テーブル作成
User.sync();
Team.sync();
TeamUser.sync();
UserAccount.sync();

module.exports = {
  User: User,
  Team: Team,
  TeamUser: TeamUser,
  UserAccount: UserAccount
};
```

`belongsToMany` を使用して中間テーブルを介したモデルを構築する際は必ず `through` を記載する。  
`belongsToMany` でリレーションを構築するとユーザモデルから以下の関数を実行できるようになる。（チームモデルも同様）

- user.addTeam
    - 既存のteamテーブルのteamモデル（配列も可）をuserと関連付け。
- user.addTeams
    - 既存のteamテーブルのteamモデル（配列）をuserと関連付け。
- user.countTeams
    - userに関連付けされたteamを数え上げる。
- user.createTeam
    - teamオブジェクトを渡して、新規にteamテーブルにteamレコードを作成して且つ関連付け。
- user.getTeams
    - userと関連付けされたteamモデルを取得する。
- user.hasTeam
    - userに引数で与えたteamモデルが関連付けされているか確認する。（true/false）
- user.hasTeams
    - userに引数で与えたteamモデルが関連付けされているか確認する。（true/false）
- user.removeTeam
    - userと引数で与えたteamモデル（配列も可）の関連付けを削除する。
    - ただし、teamモデルは削除されない。
- user.removeTeams
    - userと引数で与えたteamモデル（配列）の関連付けを削除する。
    - ただし、teamモデルは削除されない。
- user.setTeams
    - **add との違いがわかってない。。。**

[参考](http://docs.sequelizejs.com/class/lib/associations/belongs-to-many.js~BelongsToMany.html)

### app/app.js

```javascript
const db = require('./repositories/db');
const User = db.User;
const Team = db.Team;

User.create({
  user_id: '0001',
  user_name: 'user0001'
}).then(() => {
  User.findOne({
    where:{user_id: '0001'}
  }).then((user) => {
    console.log("user_name: " + user.user_name);
    user.createTeam({
      team_id: `0001`,
      team_name: 'team0001'
    }).then(() => {
      user.countTeams().then((num) => {
        console.log("team num of user: " + num);
      }).then(() => {
        user.getTeams().then((teams) => {
          console.log("teams: " + teams[0].dataValues.team_name);
          Team.create({
            team_id: '0002',
            team_name: 'team0002'
          }).then(() => {
            Team.findOne({
              where: {team_id: '0002'}
            }).then((team) => {
              console.log("team_name: " + team.dataValues.team_name);
              user.addTeam(team).then(() => {
                user.countTeams().then((num) => {
                  console.log("team num of user: " + num);
                }).then(() => {
                  let teams = [];
                  teams.push(team);
                  user.removeTeams(teams).then(() => {
                    user.countTeams().then((num) => {
                      console.log("team num of user: " + num);
                    });
                  });
                });
              });
            });
          });
        })
      });
    });
  });
});
```

# おすすめ書籍

<!-- amazon affiliate kindle node.js --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B08HRMTXHB&linkId=f02e6af82c7864b6df5fd31c0639d4bf"></iframe>