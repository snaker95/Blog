---
title: js滚动加载
categories: [JavaScript]
tags: JavaScript
keywords: [HTML,JS,滚动加载]
description: 随着智能设备越来越普及, 从原先的`分页加载` 发展到现在`滚动加载`, 都为的是提高用户的体验效果; 一切为了`美`;
---

[TOC]


# js滚动加载
随着智能设备越来越普及, 从原先的`分页加载` 发展到现在`滚动加载`, 都为的是提高用户的体验效果; 一切为了`美`;
## js代码
```js
(function(window, document, undefined) {
    var $window = $(window);
    /* Convenience methods in jQuery namespace. */
    /* Use as  $.belowthefold(element, {threshold : 100, container : window}) */

    $.belowthefold = function(element, settings) {
        var fold;
        if (settings.container === undefined || settings.container === window) {
            fold = window.innerHeight ? window.innerHeight : $window.height();
            fold += $window.scrollTop();
        } else {
            fold = $(settings.container).offset().top + $(settings.container).height();
        }
        return fold <= $(element).offset().top - settings.threshold;
    };

    $.rightoffold = function(element, settings) {
        var fold;

        if (settings.container === undefined || settings.container === window) {
            fold = $window.width() + $window.scrollLeft();
        } else {
            fold = $(settings.container).offset().left + $(settings.container).width();
        }

        return fold <= $(element).offset().left - settings.threshold;
    };

    $.abovethetop = function(element, settings) {
        var fold;

        if (settings.container === undefined || settings.container === window) {
            fold = $window.scrollTop();
        } else {
            fold = $(settings.container).offset().top;
        }

        return fold >= $(element).offset().top + settings.threshold  + $(element).height();
    };

    $.leftofbegin = function(element, settings) {
        var fold;

        if (settings.container === undefined || settings.container === window) {
            fold = $window.scrollLeft();
        } else {
            fold = $(settings.container).offset().left;
        }

        return fold >= $(element).offset().left + settings.threshold + $(element).width();
    };

    $.inviewport = function(element, settings) {
         return !$.rightoffold(element, settings) && !$.leftofbegin(element, settings) &&
                !$.belowthefold(element, settings) && !$.abovethetop(element, settings);
     };
    
    /** ---- 使用案例
     *  $elements.children().last().nextpage({callback:function(){
     *      $scfg['s_type'] = 'insert';
     *      $acfg['url'] = $acfg['url'].replace(/.html/,'/p/'+nextPage+'.html');
     *      $.ajax_list($acfg, $scfg, complete)
     *  }}); 
     *  // 手动加载下一页
     *  $elements.children().trigger("next");     *  $elements.children().next();
     **/
    $.fn.nextpage = function(options) {
        var elements = this;
        var $container;
        var settings = {
            threshold       : 0,
            failure_limit   : 0,
            event           : "scroll",
            effect          : "show",
            container       : window,
            data_attribute  : "original",
            skip_invisible  : true,
            appear          : null,
            loaded          : false,
            handnext        : 'next', // 手动加载下一页
            callback        : null
        };

        function update() {
            var counter = 0;

            elements.each(function() {
                var $this = $(this);
                if (settings.skip_invisible 
                    && ($this.css("visibility") != "visible" 
                    || $this.css("display") == "none")
                ) {
                    return;
                }
                if ($.abovethetop(this, settings) ||$.leftofbegin(this, settings)
                    ) {
                        /* Nothing. */
                } else if (!$.belowthefold(this, settings) && !$.rightoffold(this, settings)
                            ) {
                        $this.trigger("appear");
                        /* if we found an image we'll load, reset the counter */
                        counter = 0;
                } else {
                    if (++counter > settings.failure_limit) {
                        return false;
                    }
                }
            });

        }

        if(options) {
            /* Maintain BC for a couple of versions. */
            if (undefined !== options.failurelimit) {
                options.failure_limit = options.failurelimit;
                delete options.failurelimit;
            }
            if (undefined !== options.effectspeed) {
                options.effect_speed = options.effectspeed;
                delete options.effectspeed;
            }

            $.extend(settings, options);
        }

        /* Cache container as jQuery as object. */
        $container = (settings.container === undefined) ? $window : $(settings.container);

        /* Fire one scroll event per scroll. Not one scroll event per image. */
        if (0 === settings.event.indexOf("scroll")) {
            $container.bind(settings.event, function() {
                return update();
            });
        }

        this.each(function() {
            var self = this;
            var $self = $(self);

            self.loaded = settings.loaded;

            /* When appear is triggered load original image. */
            $self.one("appear", function() {
                if (!this.loaded) {
                    if (settings.appear) {
                        var elements_left = elements.length;
                        settings.appear.call(self, elements_left, settings);
                    }
                    // callback function
                    return settings.callback();
                }
            });

            /* When wanted event is triggered load original image */
            /* by triggering appear.                              */
            if (0 !== settings.event.indexOf("scroll")) {
                $self.bind(settings.event, function() {
                    if (!self.loaded) {
                        $self.trigger("appear");
                    }
                });
            }

            if ( 0 === settings.handnext.indexOf("next")) {
                $container.bind(settings.handnext, function() {
                    if (!self.loaded) {
                        $self.trigger("appear");
                    }
                });
            }
        });

        /* With IOS5 force loading images when navigating with back button. */
        /* Non optimal workaround. */
        if ((/(?:iphone|ipod|ipad).*os 5/gi).test(navigator.appVersion)) {
            $window.bind("pageshow", function(event) {
                if (event.originalEvent && event.originalEvent.persisted) {
                    elements.each(function() {
                        $(this).trigger("appear");
                    });
                }
            });
        }

        /* Force initial check if images should appear. */
        $(document).ready(function() {
            update();
        });
    };
})(window, document);
```
* 详细可见: [Snaker95`GitHub](https://github.com/snaker95/JS/blob/master/scrollpage/js/nextajax.js)

Author [@Snaker95][1]

[1]: http://www.sharedsea.com





