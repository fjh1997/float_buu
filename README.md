# float_buu
CTFd中文主题，高仿buuoj.cn以及ctf.hzyxxl.com

## reqiurements

需要api/v1/challenges/solves支持

在/CTFd/CTFd/api/v1/challenges.py中加入如下几行,也可以直接git apply 补丁：

```python
from sqlalchemy import func
```



```python
@challenges_namespace.route("/solves")
class AllChallengeSolves(Resource):
    @check_challenge_visibility
    @check_score_visibility
    @during_ctf_time_only
    @require_verified_emails
    def get(self):
        Model = get_model()

        response = {}
        challenges = (
            db.session.query(Solves.challenge_id, func.count(Solves.challenge_id))
                .join(Model, Solves.account_id == Model.id)
                .filter(
                Model.banned == False,
                Model.hidden == False
            )
                .group_by(Solves.challenge_id)
                .all()
        )

        for challenge in challenges:
            response[str(challenge[0])] = challenge[1]

        return {"success": True, "data": response}

```



## 安装

需要回滚到CTFd以前的版本

```bash
git clone https://github.com/CTFd/CTFd.git
cd CTFd/
git reset 6c5c63d667a17aec159c8e26ea53dccfbc4d0fa3 --hard
git appply 0001-solves-api.patch
cd CTFd/themes
git clone https://github.com/fjh1997/float_buu.git
```

