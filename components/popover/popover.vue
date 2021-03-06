<template lang="html">
<slot name="trigger"></slot>
<div class="ant-popover ant-popover-placement-{{ placement }}" @click.stop transition="fade" v-show="visible">
    <div class="ant-popover-content">
        <div class="ant-popover-arrow"></div>
        <div class="ant-popover-inner">
            <div class="ant-popover-title" v-if="title != ''">{{ title }}</div>
            <div class="ant-popover-inner-content">
                <template v-if="content">{{ content }}</template>
                <template v-else>
                    <slot name="content"></slot>
                </template>
            </div>
        </div>
    </div>
</div>
</template>

<script>
import { defaultProps, addClass, removeClass, addStyle } from '../../utils';

const $body = document.body;

export default {
    name: 'popover',
    props: defaultProps({
        title: '',
        placement: 'bottom',
        visible: false,
        trigger: 'click',
        content: '',//传递了content,则忽略content插槽
        onVisibleChange: ()=>{},
        openClassName: '',
        disabled: false,//临时禁用弹出气泡
        skip: false,//跳过弹出气泡,直接执行actionFunc
        skipFunc: ()=>{}//供hover和click交互触发此函数
    }),
    data: function(){
        return {

        }
    },
    ready: function(){
        const _this = this;
        const $trigger = this.$el.nextElementSibling;
        const $popover = $trigger.nextElementSibling;
        const getOffset = function(el) {
            let x = 0;
            let y = 0;
            let ele = el;
            while (ele) {
                x += parseInt(ele.offsetLeft, 10);
                y += parseInt(ele.offsetTop, 10);
                ele = ele.offsetParent;
            }
            return { top: y, left: x };
        };
        _this.popShow = function(){
            let leftFactor = 0.5;//偏移0.5个$trigger自身宽度
            let topFactor = 0.5;//偏移0.5个$trigger自身高度
            const offset = getOffset($trigger);
            const eleWidth = $trigger.offsetWidth;
            const eleHeight = $trigger.offsetHeight;
            if (_this.placement.toUpperCase().includes('LEFT')){
                leftFactor = 0;
            } else if (_this.placement.toUpperCase().includes('RIGHT')){
                leftFactor = 1;
            }

            if (_this.placement.toUpperCase().includes('TOP')){
                topFactor = 0;
            } else if (this.placement.toUpperCase().includes('BOTTOM')){
                topFactor = 1;
            }

            addStyle($popover, {
                left: `${offset.left + (eleWidth * leftFactor)}px`,
                top: `${offset.top + (eleHeight * topFactor)}px`
            });
            _this.visible = true;
            if(_this.openClassName){
                addClass($trigger, _this.openClassName);
            }
        };
        _this.popHide = function () {
            _this.visible = false;
            if(_this.openClassName){
                removeClass($trigger, _this.openClassName);
            }
        };

        if(_this.trigger === 'click'){
            $trigger.addEventListener('click', function(e){
                e.stopPropagation();
                if(_this.disabled){
                    return;
                }
                if(_this.skip){
                    try {
                        _this.skipFunc();
                    }catch(e){console && console.log(e)}
                    return;
                }
                _this.visible = !_this.visible;
            }, false);
            _this.bodyClick = function(){
                _this.visible = false;
            };
            $body.addEventListener('click', _this.bodyClick, false);
        }
        if(_this.trigger === 'hover'){
            let hover_timer = null;
            let delay = 200;
            $trigger.addEventListener('mouseover', function(e){
                if(_this.disabled){
                    return;
                }
                clearTimeout(hover_timer);
                if(_this.skip){//仅click和mouseover时才触发skipFunc
                    try {
                        _this.skipFunc();
                    }catch(e){console && console.log(e)}
                    return;
                }
                if(!_this.visible){
                    _this.visible = true;
                }
            }, false);
            $trigger.addEventListener('mouseout', function(e){
                hover_timer = setTimeout(function () {
                    _this.visible = false;
                }, delay);
            }, false);
            $popover.addEventListener('mouseover', function () {
                clearTimeout(hover_timer);
            }, false);
            $popover.addEventListener('mouseout', function () {
                hover_timer = setTimeout(function(){
                    _this.visible = false;
                }, delay);
            }, false);
        }


        if(_this.trigger === 'focus'){
            $trigger.addEventListener('focus', function(e){
                if(_this.disabled){
                    return;
                }
                _this.visible = true;
            }, false);
            $trigger.addEventListener('blur', function(e){
                if(_this.disabled){
                    return;
                }
                _this.visible = false;
            }, false);
        }


        _this.$nextTick(function () {
            if(_this.disabled){
                return;
            }
            _this.visible ? _this.popShow() : _this.popHide();
        });

        $body.appendChild($popover);

    },
    watch: {
        'visible': function (val) {
            if(val === true){
                this.popShow();
            }else{
                this.popHide();
            }
            this.onVisibleChange(this.visible);
        }
    },
    destroyed: function(){
        if(this.trigger === 'click'){
            $body.removeEventListener('click', this.bodyClick);
        }
    }
}
</script>
