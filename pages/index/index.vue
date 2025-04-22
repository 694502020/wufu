<template>
  <view class="container">
    <!-- 顶部搜索区域 -->
    <view class="search-area">
      <view class="search-box">
        <text class="search-icon">🔍</text>
        <input 
          class="search-input" 
          v-model="searchValue" 
          placeholder="搜索你感兴趣的内容" 
          @keypress.enter="search"
        />
      </view>
      <button class="search-button" @click="search">搜索</button>
    </view>

    <!-- 导航链接区域 -->
    <view class="nav-container">
      <view class="nav-title">
        <text>常用网站导航</text>
      </view>
      <view class="nav-links">
        <view
          v-for="(link, index) in navLinks"
          :key="index"
          class="nav-link"
          @click="goToUrl(link.url)"
          :class="{ 'custom-link': link.isCustom }"
        >
          <image :src="link.icon" class="nav-icon"></image>
          <text class="nav-text">{{ link.title }}</text>
        </view>
        
        <!-- 添加自定义导航按钮 -->
        <view class="nav-link add-link" @click="showAddModal = true">
          <view class="add-icon">+</view>
          <text class="nav-text">自定义添加</text>
        </view>
      </view>
    </view>

    <!-- 添加自定义导航的弹窗 -->
    <view class="modal-overlay" v-if="showAddModal" @click="showAddModal = false">
      <view class="modal-content" @click.stop>
        <view class="modal-header">
          <text class="modal-title">添加自定义导航</text>
          <text class="modal-close" @click="showAddModal = false">×</text>
        </view>
        
        <view class="modal-body">
          <view class="form-item">
            <text class="form-label">网站名称</text>
            <input v-model="newLink.title" placeholder="请输入网站名称" class="form-input" />
          </view>
          
          <view class="form-item">
            <text class="form-label">网站地址</text>
            <input v-model="newLink.url" placeholder="请输入以http://或https://开头的网址" class="form-input" />
          </view>
          
          <view class="form-item">
            <text class="form-label">图标选择</text>
            <view class="icon-selector">
              <view 
                v-for="(icon, idx) in defaultIcons" 
                :key="idx"
                class="icon-option"
                :class="{ 'selected': newLink.icon === icon }"
                @click="newLink.icon = icon"
              >
                <image :src="icon" class="icon-preview"></image>
              </view>
            </view>
          </view>
          
          <button class="add-button" @click="addCustomLink">添加</button>
        </view>
      </view>
    </view>
  </view>
</template>

<script>
export default {
  data() {
    return {
      searchValue: '',
      showAddModal: false,
      newLink: {
        title: '',
        url: '',
        icon: '/static/logo.png',
        isCustom: true
      },
      defaultIcons: [
        '/static/logo.png',
        '/static/39jiankan.png',
        '/static/bohe.png',
        '/static/biying.png',
        '/static/chufang.png',
        '/static/zhihu.png'
      ],
      navLinks: [
        {
          icon: '/static/douyin.png',
          title: '抖音',
          url: 'https://www.douyin.com/',
          isCustom: false
        },
        {
          icon: '/static/taobao.png',
          title: '淘宝',
          url: 'https://www.taobao.com/',
          isCustom: false
        },
        {
          icon: '/static/jd.png',
          title: '京东',
          url: 'https://www.jd.com',
          isCustom: false
        },
        {
          icon: '/static/fujian.png',
          title: '福健网',
          url: 'http://www.fjplus.cn',
          isCustom: false
        },
        {
          icon: '/static/sohu.png',
          title: '搜狐健康',
          url: 'http://health.sohu.com/',
          isCustom: false
        },
        {
          icon: '/static/weibo.png',
          title: '微博',
          url: 'https://weibo.com/',
          isCustom: false
        },
        {
          icon: '/static/mgtv.png',
          title: '芒果TV',
          url: 'https://www.mgtv.com/',
          isCustom: false
        },
        {
          icon: '/static/iqiyi.png',
          title: '爱奇艺',
          url: 'http://www.iqiyi.com',
          isCustom: false
        },
        {
          icon: '/static/youku.png',
          title: '优酷',
          url: 'http://www.youku.com',
          isCustom: false
        },
        {
          icon: '/static/chufang.png',
          title: '下厨房',
          url: 'https://www.xiachufang.com',
          isCustom: false
        },
        {
          icon: '/static/vip.png',
          title: '唯品会',
          url: 'https://www.vip.com/',
          isCustom: false
        },
        {
          icon: '/static/mama.png',
          title: '妈妈网',
          url: 'http://www.mama.cn/',
          isCustom: false
        },
        {
          icon: '/static/pclady.png',
          title: 'PCLady',
          url: 'http://www.pclady.com.cn/',
          isCustom: false
        },
        {
          icon: '/static/rayli.png',
          title: '瑞丽时尚',
          url: 'https://www.rayli.com.cn/channel/video/shishang',
          isCustom: false
        },
        {
          icon: '/static/mogu.png',
          title: '蘑菇街',
          url: 'https://m.mogu.com/?ptp=32.v5mL0b.0.0.GPPXOpre',
          isCustom: false
        }
      ]
    };
  },
  onLoad() {
    // 从本地存储加载自定义链接
    try {
      const customLinks = uni.getStorageSync('customNavLinks');
      if (customLinks) {
        this.navLinks = [...this.navLinks, ...JSON.parse(customLinks)];
      }
    } catch (e) {
      console.error('Failed to load custom links:', e);
    }
  },
  methods: {
    search() {
      if (!this.searchValue.trim()) return;
      
      const searchKeyword = this.searchValue;
      const bingSearchUrl = `https://www.bing.com/search?q=${encodeURIComponent(searchKeyword)}`;
      
      // 在uni-app中，使用plus.runtime.openURL或uni.navigateTo等方法
      // 根据实际运行环境选择
      // 网页环境
      if (typeof window !== 'undefined') {
        window.open(bingSearchUrl, '_blank');
      } 
      // App环境
      else if (typeof plus !== 'undefined') {
        plus.runtime.openURL(bingSearchUrl);
      }
    },
    
    goToUrl(url) {
      // 同样根据环境选择打开方式
      if (typeof window !== 'undefined') {
        window.open(url, '_blank');
      } else if (typeof plus !== 'undefined') {
        plus.runtime.openURL(url);
      }
    },
    
    addCustomLink() {
      // 基本验证
      if (!this.newLink.title.trim()) {
        uni.showToast({
          title: '请输入网站名称',
          icon: 'none'
        });
        return;
      }
      
      if (!this.newLink.url.trim() || 
          (!this.newLink.url.startsWith('http://') && !this.newLink.url.startsWith('https://'))) {
        uni.showToast({
          title: '请输入有效的网址',
          icon: 'none'
        });
        return;
      }
      
      // 创建新链接副本并添加到列表
      const newCustomLink = {
        icon: this.newLink.icon,
        title: this.newLink.title,
        url: this.newLink.url,
        isCustom: true
      };
      
      this.navLinks.push(newCustomLink);
      
      // 保存到本地存储
      try {
        const customLinks = this.navLinks.filter(link => link.isCustom);
        uni.setStorageSync('customNavLinks', JSON.stringify(customLinks));
      } catch (e) {
        console.error('Failed to save custom links:', e);
      }
      
      // 重置表单并关闭弹窗
      this.newLink = {
        title: '',
        url: '',
        icon: '/static/logo.png',
        isCustom: true
      };
      
      this.showAddModal = false;
      
      uni.showToast({
        title: '添加成功',
        icon: 'success'
      });
    }
  }
};
</script>

<style>
.container {
  padding: 20px;
  background-color: #f8f9fa;
  min-height: 100vh;
}

/* 搜索区域样式 */
.search-area {
  display: flex;
  align-items: center;
  margin-bottom: 30px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
  border-radius: 8px;
  background: #fff;
  padding: 8px;
}

.search-box {
  display: flex;
  align-items: center;
  flex: 1;
  background-color: #f5f5f5;
  border-radius: 6px;
  padding: 0 10px;
  margin-right: 10px;
}

.search-icon {
  margin-right: 10px;
  font-size: 18px;
  color: #999;
}

.search-input {
  flex: 1;
  height: 40px;
  font-size: 16px;
  border: none;
  background: transparent;
}

.search-button {
  background-color: #3e99f5;
  color: white;
  border: none;
  border-radius: 6px;
  padding: 0 20px;
  height: 40px;
  line-height: 40px;
  font-size: 16px;
}

/* 导航区域样式 */
.nav-container {
  background-color: #fff;
  border-radius: 8px;
  padding: 20px;
  box-shadow: 0 2px 10px rgba(0, 0, 0, 0.05);
}

.nav-title {
  margin-bottom: 20px;
  border-left: 4px solid #3e99f5;
  padding-left: 10px;
}

.nav-title text {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.nav-links {
  display: flex;
  flex-wrap: wrap;
  justify-content: flex-start;
}

.nav-link {
  width: 25%;
  display: flex;
  flex-direction: column;
  align-items: center;
  margin-bottom: 25px;
  position: relative;
}

.nav-icon {
  width: 50px;
  height: 50px;
  border-radius: 10px;
  margin-bottom: 8px;
  background-color: #f5f5f5;
}

.nav-text {
  font-size: 14px;
  color: #333;
  text-align: center;
  width: 80%;
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

.add-icon {
  width: 50px;
  height: 50px;
  border-radius: 10px;
  background-color: #f5f5f5;
  display: flex;
  align-items: center;
  justify-content: center;
  font-size: 24px;
  color: #999;
  margin-bottom: 8px;
  border: 1px dashed #ddd;
}

.custom-link::after {
  content: "自定义";
  position: absolute;
  top: -5px;
  right: 20px;
  background-color: #ffaa00;
  color: white;
  font-size: 10px;
  padding: 2px 4px;
  border-radius: 4px;
}

/* 弹窗样式 */
.modal-overlay {
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  background-color: rgba(0, 0, 0, 0.6);
  display: flex;
  justify-content: center;
  align-items: center;
  z-index: 999;
}

.modal-content {
  width: 80%;
  background-color: #fff;
  border-radius: 10px;
  overflow: hidden;
}

.modal-header {
  padding: 15px;
  display: flex;
  justify-content: space-between;
  align-items: center;
  border-bottom: 1px solid #eee;
}

.modal-title {
  font-size: 18px;
  font-weight: bold;
  color: #333;
}

.modal-close {
  font-size: 22px;
  color: #999;
}

.modal-body {
  padding: 20px;
}

.form-item {
  margin-bottom: 20px;
}

.form-label {
  display: block;
  margin-bottom: 8px;
  color: #333;
  font-size: 16px;
}

.form-input {
  width: 100%;
  height: 40px;
  border: 1px solid #ddd;
  border-radius: 4px;
  padding: 0 10px;
  font-size: 14px;
}

.icon-selector {
  display: flex;
  flex-wrap: wrap;
  gap: 10px;
  margin-top: 10px;
}

.icon-option {
  width: 40px;
  height: 40px;
  border-radius: 6px;
  border: 1px solid #eee;
  overflow: hidden;
  display: flex;
  justify-content: center;
  align-items: center;
}

.icon-option.selected {
  border: 2px solid #3e99f5;
}

.icon-preview {
  width: 30px;
  height: 30px;
}

.add-button {
  background-color: #3e99f5;
  color: white;
  width: 100%;
  height: 44px;
  border: none;
  border-radius: 6px;
  font-size: 16px;
  margin-top: 10px;
}

/* 响应式布局 */
@media screen and (max-width: 480px) {
  .nav-link {
    width: 33.33%;
  }
}

@media screen and (max-width: 375px) {
  .nav-link {
    width: 50%;
  }
  
  .search-area {
    flex-direction: column;
  }
  
  .search-box {
    width: 100%;
    margin-right: 0;
    margin-bottom: 10px;
  }
  
  .search-button {
    width: 100%;
  }
}
</style>