<template>
    <div class="register-container">
        <!-- 使用渐变背景 -->
        <div class="register-panel">
            <div class="logo">
                <Logo sysName="没账号？立即注册" />
            </div>
            <div class="text">
                <input v-model="act" class="act" placeholder="注册账号" />
            </div>
            <div class="text">
                <input v-model="name" class="act" placeholder="用户名" />
            </div>
            <div class="text">
                <input v-model="pwd" class="pwd" type="password" placeholder="输入密码" />
            </div>
            <div class="text">
                <input v-model="pwdConfirm" class="pwd" type="password" placeholder="输入密码" />
            </div>
            <div style="text-align: center;">
                <span class="register-btn" @click="registerFunc">立即注册</span>
            </div>
            <div class="tip">
                <p>已有账户？<span class="no-act" @click="toDoLogin">返回登录</span></p>
            </div>
        </div>
    </div>
</template>

<script>
const DELAY_TIME = 1300;
import request from "@/utils/request.js";
import md5 from 'js-md5';
import Logo from '@/components/Logo.vue';
export default {
    name: "Register",
    components: { Logo },
    data() {
        return {
            act: '', // 账号
            pwd: '', // 密码
            pwdConfirm: '', // 确认密码
            name: '' // 用户名
        }
    },
    methods: {
        // 返回登录页面
        toDoLogin() {
            this.$router.push('/login');
        },
        async registerFunc() {
            if (!this.act || !this.pwd || !this.pwdConfirm || !this.name) {
                this.$swal.fire({
                    title: '填写校验',
                    text: '账号或密码或用户名不能为空',
                    icon: 'error',
                    showConfirmButton: false,
                    timer: DELAY_TIME,
                });
                return;
            }
            if (this.pwd !== this.pwdConfirm) {
                this.$swal.fire({
                    title: '填写校验',
                    text: '前后密码输入不一致',
                    icon: 'error',
                    showConfirmButton: false,
                    timer: DELAY_TIME,
                });
                return;
            }
            const hashedPwd = md5(md5(this.pwd));
            const paramDTO = { userAccount: this.act, userPwd: hashedPwd, userName: this.name };
            try {
                const { data } = await request.post(`user/register`, paramDTO);
                if (data.code !== 200) {
                    this.$swal.fire({
                        title: '注册失败',
                        text: data.msg,
                        icon: 'error',
                        showConfirmButton: false,
                        timer: DELAY_TIME,
                    });
                    return;
                }
                // 使用Swal通知注册成功，延迟后跳转
                this.$swal.fire({
                    title: '注册成功',
                    text: '即将返回登录页...',
                    icon: 'success',
                    showConfirmButton: false,
                    timer: DELAY_TIME,
                });
                // 根据角色延迟跳转
                setTimeout(() => {
                    this.$router.push('/login');
                }, DELAY_TIME);
            } catch (error) {
                console.error('注册请求错误:', error);
            }
        }
    }
};
</script>

<style lang="scss" scoped>
* {
    user-select: none;
    box-sizing: border-box;
}

.register-container {
    background: linear-gradient(120deg, #a1c4fd 0%, #c2e9fb 100%);
    width: 100%;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;
}

.register-panel {
    background: white;
    padding: 3rem;
    border-radius: 1rem;
    box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
    animation: slideIn 0.5s ease-out;
    width: 380px;
}

.logo {
    text-align: center;
    margin-bottom: 2rem;
}

.text {
    margin-bottom: 1.5rem;
}

.act,
.pwd {
    width: 100%;
    padding: 1rem;
    border: 2px solid #e0e0e0;
    border-radius: 0.5rem;
    font-size: 1rem;
    transition: border-color 0.3s ease;

    &:focus {
        outline: none;
        border-color: #42a5f5;
    }
}

.register-btn {
    display: block;
    width: 100%;
    padding: 1rem;
    background-color: #42a5f5;
    color: white;
    border: none;
    border-radius: 0.5rem;
    font-size: 1rem;
    font-weight: bold;
    cursor: pointer;
    transition: background-color 0.3s ease;

    &:hover {
        background-color: #1976d2;
    }
}

.tip {
    margin-top: 1.5rem;
    text-align: center;
    font-size: 0.9rem;

    .no-act {
        color: #42a5f5;
        text-decoration: none;
        cursor: pointer;
        transition: color 0.3s ease;

        &:hover {
            color: #1976d2;
        }
    }
}

@keyframes slideIn {
    from {
        opacity: 0;
        transform: translateY(-20px);
    }
    to {
        opacity: 1;
        transform: translateY(0);
    }
}
</style>
