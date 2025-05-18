<template>
    <div class="login-container">
        <!-- 使用渐变背景 -->
        <div class="login-panel">
            <div class="logo">
                <Logo :bag="colorLogo" sysName="安馨智享"/>
            </div>
            <div class="text">
                <input v-model="act" class="act" placeholder="账号" />
            </div>
            <div class="text">
                <input v-model="pwd" class="pwd" type="password" placeholder="密码" />
            </div>
            <div>
                <span class="login-btn" @click="login">立即登录</span>
            </div>
            <div class="tip">
                <p>没有账号？<span class="no-act" @click="toDoRegister">点此注册</span></p>
            </div>
        </div>
    </div>
</template>

<script>
const ADMIN_ROLE = 1;
const USER_ROLE = 2;
const DELAY_TIME = 1300;
import request from "@/utils/request.js";
import { setToken } from "@/utils/storage.js";
import md5 from 'js-md5';
import Logo from '@/components/Logo.vue';
export default {
    name: "Login",
    components: { Logo },
    data() {
        return {
            act: '',
            pwd: '',
            colorLogo: 'rgb(38,38,38)',
        }
    },
    methods: {
        // 跳转注册页面
        toDoRegister(){
            this.$router.push('/register');
        },
        async login() {
            if (!this.act || !this.pwd) {
                this.$swal.fire({
                    title: '填写校验',
                    text: '账号或密码不能为空',
                    icon: 'error',
                    showConfirmButton: false,
                    timer: DELAY_TIME,
                });
                return;
            }
            const hashedPwd = md5(md5(this.pwd));
            const paramDTO = { userAccount: this.act, userPwd: hashedPwd };
            try {
                const { data } = await request.post(`user/login`, paramDTO);
                if (data.code !== 200) {
                    this.$swal.fire({
                        title: '登录失败',
                        text: data.msg,
                        icon: 'error',
                        showConfirmButton: false,
                        timer: DELAY_TIME,
                    });
                    return;
                }
                setToken(data.data.token);
                setTimeout(() => {
                    const { role } = data.data;
                    this.navigateToRole(role);
                }, DELAY_TIME);
            } catch (error) {
                console.error('登录请求错误:', error);
                this.$message.error('登录请求出错，请重试！');
            }
        },
        navigateToRole(role) {
            switch (role) {
                case ADMIN_ROLE:
                    this.$router.push('/admin');
                    break;
                case USER_ROLE:
                    this.$router.push('/user');
                    break;
                default:
                    console.warn('未知的角色类型:', role);
                    break;
            }
        },
    }
};
</script>

<style lang="scss" scoped>
* {
    user-select: none;
    box-sizing: border-box;
}

.login-container {
    // 使用渐变背景
    background: linear-gradient(135deg, #667eea 0%, #764ba2 100%); 
    width: 100%;
    min-height: 100vh;
    display: flex;
    justify-content: center;
    align-items: center;

    .login-panel {
        // 增加面板宽度和内边距
        width: 350px; 
        padding: 40px;
        border-radius: 16px;
        background-color: rgba(255, 255, 255, 0.95);
        box-shadow: 0 10px 25px rgba(0, 0, 0, 0.1);
        // 添加动画效果
        animation: fadeIn 0.5s ease-out; 

        .logo {
            margin: 0 0 30px 0;
            text-align: center;
        }

        .act,
        .pwd {
            margin: 15px 0;
            height: 48px;
            width: 100%;
            padding: 0 16px;
            background-color: #f5f5f5;
            border: 2px solid transparent;
            border-radius: 8px;
            font-weight: 500;
            font-size: 16px;
            transition: border-color 0.3s ease;

            &:focus {
                outline: none;
                border-color: #667eea;
                background-color: #fff;
            }
        }

        .login-btn {
            display: block;
            text-align: center;
            border-radius: 8px;
            margin-top: 30px;
            height: 48px;
            line-height: 48px;
            width: 100%;
            // 更改按钮颜色
            background-color: #667eea; 
            font-size: 16px;
            font-weight: 600;
            border: none;
            color: #fff;
            cursor: pointer;
            transition: background-color 0.3s ease;

            &:hover {
                background-color: #5a67d8;
            }
        }

        .tip {
            margin: 20px 0 0 0;
            text-align: center;

            p {
                font-size: 14px;
                color: #6b7280;

                .no-act {
                    color: #667eea;
                    text-decoration: none;
                    transition: color 0.3s ease;

                    &:hover {
                        color: #5a67d8;
                    }
                }
            }
        }
    }
}

@keyframes fadeIn {
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
