"use strict";
// FUNCTIONS
//
//
var queueRunner = function(e, array) {
	array.forEach(function(elem, index) {
		elem.call();
	});
};
var limit_slider_height = function() {
	var slides = $(".slides");
	slides.css("max-height", slides.attr("height") + "px");
};
var defineImageListSize = function() {
	var className = "little-info";
	var items = $(".uss_catalog_list_cat > .uss_catalog_category,.uss_shop_list_cat > .uss_shop_category,.uss_eshop_sameproducts.list  .item");
	var item = items.eq(0);
	var itemSize = parseInt(item.css("width"));
	var imageSize = parseInt(
		item
			.find(".uss_catalog_cat_img,.uss_shop_cat_img_wrap,.imageWrapOuter")
			.eq(0)
			.css("width")
	);
	if (imageSize >= (itemSize / 100) * 66) {
		items.addClass(className);
	} else {
		items.removeClass(className);
	}
};

function _isTouch() {
	// функция проверки поддержки браузером событий TouchEvent (проверка на мобильное устройство)
	try {
		document.createEvent("TouchEvent");
		return true;
	} catch (e) {
		return false;
	}
}

function ussAnchor(target, scrollSpeed, delta) {
	// функция плавного перехода к якорям
	// атрибут href ссылки должен содержать id блока для перехода , соответственно на странице должен быть блок с указанным id
	// пример : href="#contacts"
	// scrollSpeed - можно задать скорость скролла в вызове функции (не обязательно)
	// delta - дополнительный отступ , используется когда есть фиксированное меню по вернему краю и для других подобных ситуаций. (не обязательно)
	// пример : ussAnchor('.menu .anchor');
	// пример2 : ussAnchor('.menu .anchor',700);
	// пример3 : ussAnchor('.menu .anchor',null,68);
	var $root = $("html, body");
	$(document).on("click", target, function(e) {
		$(e.target).parents('.open').removeClass('open')
		$root.animate(
			{
				scrollTop: $($.attr(this, "href")).offset().top - delta || 0,
			},
			scrollSpeed || 500
		);
		return false;
	});
}

function ussClicker(target, click_class) {
	// функция для перехвата кликов на touch устройствах
	// пример:  ussClicker('.menu a');
	// dependency : _isTouch();
	var clickOnce = false;
	var clickClass = click_class || "clicked";
	$(target).on("click", function(e) {
		var status = true;
		if (_isTouch()) {
			// проверяем поддерживается ли событие TouchEvent
			if (
				$(e.currentTarget)
					.parent("li")
					.hasClass(clickClass)
			) {
				// если у родителя ссылки есть класс $clickClass => переходим поссылке
				status = true;
			} else {
				// у родителя нет класса $clickClass
				if ($(e.currentTarget).siblings("ul.submenu").length > 0) {
					// у родителя нет ссылки и рядом с ссылкой есть .submenu
					// добавляем класс $clickClass
					// отменяем переход по ссылке
					$(e.currentTarget)
					.parent("li")
					.addClass(clickClass)
					.siblings("li")
					.removeClass(clickClass);
					status = false;
					e.preventDefault();
				} else {
					// рядом с ссылкой нет .submenu => переходим по ссылке
					status = true;
				}
			}
		} else {
			status = true;
		}
		if (!clickOnce) {
			$(window).resize(function(event) {
				$("." + clickClass).removeClass(clickClass);
			});
			clickOnce = true;
		}
		return status;
	});
}
var moveItem = function(target, destinition, on, where, trigger) {
	var settings = {};
	var isTransported = false;
	settings.target = target;
	settings.destinition = destinition;
	settings.on = on;
	settings.where = where;
	settings.trigger = trigger;
	settings.className = settings.target
		.split(" ")
		.splice(-1, 1)
		.join("")
		.replace(".", "");
	/*__*/
	/*__*/
	var window_width = $(window).width();
	/*__*/
	/*__*/
	var call_trigger = function(_settings, params) {
		if (_settings.trigger) {
			$(window).trigger(_settings.trigger, [params]);
		}
	};
	var transport = function(_settings) {
		if ($(_settings.target).attr("moved") !== "true" && _settings.target && _settings.destinition) {
			isTransported = true;
			var temporary_storage = $(document.createElement("div"));
			temporary_storage.addClass(_settings.className + "-holder").insertAfter($(_settings.target));
			$(_settings.target).attr("moved", "true");
			var targetSelector = {
				after: function() {
					return $(_settings.target).insertAfter(_settings.destinition);
				},
				before: function() {
					return $(_settings.target).insertBefore(_settings.destinition);
				},
				append: function() {
					return $(_settings.target).appendTo(_settings.destinition);
				},
				prepend: function() {
					return $(_settings.target).prependTo(_settings.destinition);
				},
			};
			targetSelector[_settings.where || "other"]();
			call_trigger(_settings, "less");
		} else {
			if (!_settings.target) {
				new Error("не указанно какой объект переместить");
			}
			if (!_settings.destinition) {
				new Error("не указанно куда переместить объект");
			}
		}
	};
	var revert = function(_settings) {
		$(_settings.target).attr("moved");
		if (
			$(_settings.destinition)
				.parent("*")
				.find("." + _settings.className)
				.attr("moved") === "true"
		) {
			var holder = $("." + _settings.className + "-holder");
			if (_settings.where === "after" || _settings.where === "before") {
				$(_settings.destinition)
					.siblings("." + _settings.className)
					.attr("moved", "false");
				$(_settings.destinition)
					.siblings("." + _settings.className)
					.insertBefore(holder);
			} else if (_settings.where === "prepend" || _settings.where === "append") {
				$(_settings.destinition)
					.find("." + _settings.className)
					.attr("moved", "false");
				$(_settings.destinition)
					.find("." + _settings.className)
					.insertBefore(holder);
			}
			holder.remove();
			call_trigger(_settings, "more");
		}
	};
	var init = function(_settings) {
		if (window_width <= on) {
			transport(_settings);
		} else {
			revert(_settings);
		}
	};
	init(settings);
	$(window).on("resize", function() {
		window_width = $(window).width();
		init(settings);
	});
};
// MODULE
//
(function($, window, document, undefined) {
	$.fn.sliderHeight = function(options) {
		return $(this).each(function() {
			var window_width = $("body").outerWidth();
			var _this = $(this);
			var aspect_className, pref, intrinsic_width, intrinsic_height, container_size;
			var box_width = $(_this)
				.parent("*")
				.outerWidth();
			var settings = $.extend(
				{
					width: parseInt($(this).attr("width")),
					height: parseInt($(this).attr("height")),
					container_width: parseInt($(this).attr("container_width")) || 1200,
					fixed: false,
					loaded: false,
				},
				options || {}
			);
			var methods = {
				calculateAspectRation: function() {
					var slide_height = settings.height;
					if (settings.container_width < 1200) {
						pref = 20;
					} else {
						pref = 0;
					}
					box_width = $(_this).outerWidth();
					var image_wrap = $(_this).find(".image");
					image_wrap.each(function(index, el) {
						$(el).removeClass("width_more width_less width_fit height_more height_less height_fit aspect_rated");
						aspect_className = "";
						intrinsic_width = $(el)
							.find("img")
							.prop("naturalWidth");
						intrinsic_height = $(el)
							.find("img")
							.prop("naturalHeight");
						if (intrinsic_width > settings.width) {
							aspect_className += " width_more";
						} else if (intrinsic_width < settings.width) {
							aspect_className += " width_less";
						} else {
							aspect_className += "width_fit";
						}
						if (intrinsic_height > settings.height) {
							aspect_className += " height_more";
						} else if (intrinsic_height < settings.height) {
							aspect_className += " height_less";
						} else {
							aspect_className += " height_fit";
						}
						$(el).addClass(aspect_className);
						$(el).addClass("aspect_rated");
					});
					container_size = settings.container_width + pref;
					if (settings.width > container_size) {
						if ($(window).width > container_size) {
							slide_height = ~~((settings.width * settings.height) / settings.width);
						} else {
							slide_height = ~~((box_width * settings.height) / settings.width);
						}
					} else {
						if (window_width > settings.width) {
							slide_height = ~~((settings.width * settings.height) / settings.width);
						} else {
							slide_height = ~~(((box_width + pref) * settings.height) / settings.width);
						}
					}
					image_wrap.height(slide_height);
				},
				init: function() {
					var slides = $(_this).find(".slide");
					var images = $(_this).find("img");
					var images_loaded = 0;
					images.one("load", function() {
						images_loaded += 1;
						if (images_loaded === images.length) {
							images_loaded += 1;
							settings.loaded = true;
							methods.calculateAspectRation();
						}
					});
					images.each(function(index, el) {
						if (this.complete) {
							$(this).load(); // For jQuery < 3.0
						}
					});
				},
			};
			methods.init();
			$(window).resize(function(event) {
				window_width = $("body").outerWidth();
				methods.calculateAspectRation();
			});
			return;
		});
	};
})(jQuery, window, document);
//
(function($, window, document, undefined) {
	// Функция для оборачивания блоков в один родитель
	// возвращает массив елементов для которого была вызванна. Можно чейнить (chain), оборачивать несколько раз в том числе созданные.
	// пример :
	// 		$('.uss_eshop_sameproducts.list .item').wrapBlock({
	// 			wrapperClassName: 'price-buy_btn',
	// 			target: '.price,.addToCart',
	// 		}).wrapBlock({
	// 			target : '.price-buy_btn,.producer',
	// 			wrapperClassName : 'test-wrap'
	// 		});
	// Результат : .price и .addToCart - обернуты в div.price-buy_btn , после этого div.price-buy_btn и .producer обернуты в div.test-wrap
	// вернет массив элементов : $('.uss_eshop_sameproducts.list .item')
	// target - селекторы элементов которые нужно обернуть (обязательное свойство)
	// wrapperClassName - название класса для обертки ( не обязательное свойство , но рекомендуется)
	// wrapperTagName - тип обертки можно указать любой (div,span,a,li и тд.) (не обязательное свойство , по умолчанию div)
	// wrapperLink - для тех случаев когда wrapperTagName = 'a' , указываем ссылку. потому что у <a> всегда должна быть ссылка
	// у каждой созданной обертки есть класс отражающий кол-во детей (x2,x3,x4 и тд.)
	$.fn.wrapBlock = function(options) {
		return this.each(function() {
			var _this = $(this);
			var default_settings = {
				wrapperClassName: "uss-wrapped-block",
				wrapperTagName: "div",
				wrapperLink: null,
				target: null,
				position: {
					place: null,
					type: null, // before,after,append,prepend
				},
			};
			var settings = $.extend({}, default_settings, options);
			var parent;
			var methods = {
				isAlreadyWrapped: function() {
					var _wrapperclassName = settings.wrapperClassName
						.trim()
						.split(" ")
						.join(".");
					_wrapperclassName = "." + _wrapperclassName;
					if (_this.find(_wrapperclassName).length === 0) {
						return false;
					} else {
						return true;
					}
				},
				make: function() {
					if (settings.wrapperLink) {
						settings.wrapperTagName = "a";
					}
					parent = $(document.createElement(settings.wrapperTagName));
					parent.addClass(settings.wrapperClassName);
					if (settings.wrapperTagName === "a" || settings.wrapperLink) {
						parent.attr("href", settings.wrapperLink);
					}
				},
				wrap: function() {
					if (!this.isAlreadyWrapped()) {
						var childrensLength = _this.find($(settings.target)).length;
						parent.addClass("x" + childrensLength);
						if (settings.target && settings.target.trim().length !== 0) {
							var targets_array = settings.target.split(",");
							$.each(targets_array, function(index, el) {
								_this.children(el).appendTo(parent);
							});
							if (settings.position.place && settings.position.type) {
								var positionSelector = {
									before: function() {
										return parent.insertBefore(_this.find(settings.position.place));
									},
									after: function() {
										return parent.insertAfter(_this.find(settings.position.place));
									},
									append: function() {
										return parent.appendTo(_this.find(settings.position.place));
									},
									prepend: function() {
										return parent.prependTo(_this.find(settings.position.place));
									},
									other: function() {
										console.warn("position type for wrapBlock not defined (choose after,before,prepend or append)");
										return parent.appendTo(_this);
									},
								};
								if (childrensLength > 0) {
									positionSelector[settings.position.type || "other"];
								}
							} else {
								if (childrensLength > 0) {
									parent.appendTo(_this);
								}
							}
						}
					}
				},
				init: function() {
					if ($(_this).find("." + settings.wrapperClassName).length === 0) {
						methods.make();
						methods.wrap();
					}
					return;
				},
			};
			methods.init();
			return $(this);
		});
	};
})(jQuery, window, document);
//
(function($, window, document, undefined) {
	$.fn.addLink = function(options) {
		return this.each(function() {
			var _this = this;
			var default_settings = {
				before: true,
				after: false,
				link: null,
				findIn: null,
			};
			settings = $.extend({}, default_settings, options);
			var methods = {
				init: function() {
					return;
				},
			};
			methods.init();
			return $(this);
		});
	};
})(jQuery, window, document);
//
(function($, window, document, undefined) {
	$.fn.ussClicker = function(params) {
		var selectedClassName = params && params.class ? params.class : "clicked";
		return this.each(function(index, elem) {
			return ussClicker($(elem), selectedClassName);
		});
	};
})(jQuery, window, document);
//
(function($, window, document, undefined) {
	// !!! Для этого плагина есть стандартные стили в папке верстальщика uss-extra/css/ussModal.css
	// Плагин для создания всплывающего окна из любого контента.
	// пример : $('.modal').ussModal($('.header .modal_opener'));
	//	$('.modal') - div с классом "modal".
	//	$('.header .modal_opener') - ссылка при клике на которую появляется всплывающее окно.
	$.fn.ussModal = function(trigger) {
		return this.each(function() {
			var _this = this;
			var methods = {
				init: function() {
					$(_this).hide();
					if ($(_this).attr("uss-modal-wrapped") !== "true") {
						var modal_close = $('<div class="close"></div>').appendTo($(_this)); // создаем и добавляем кнопку закрыть
					}
					var form_wrap = $("<div></div>"); // создаем обертку для виджета
					var modal_clases = $(_this)
						.attr("class")
						.split(" ");
					modal_clases = $.map(modal_clases, function(className) {
						return className + "-wrap";
					});
					modal_clases = modal_clases.join(" ");
					form_wrap.addClass(modal_clases); // назначаем классы для обертки такие же как у контенера + прифекс "wrap"
					if ($(_this).attr("uss-modal-wrapped") !== "true") {
						// проверяем не обернут ли уже блок
						// если нет => оборачиваем
						var form = $(_this)
							.children("*")
							.wrapAll(form_wrap);
						$(_this).attr("uss-modal-wrapped", "true");
					}
					// слушаем событие click по всплывающему окну , при клике мимо обертки (form_wrap) => закрываем окно.
					// при клике на кнопку close закрываем окно
					$(_this).live("click", function(e) {
						if (e.target.className === $(_this).attr("class") || e.target.className === "close") {
							$(_this).hide();
						}
					});
					// слушаем событие click на кнопке для вызова окна.
					trigger.on("click", function(e) {
						$(_this)
							.siblings(".modal")
							.hide(); // !!! скрываем соседние модальные окна с классом modal
						$(_this).show();
						e.preventDefault();
						return false;
					});
				},
			};
			methods.init();
			return $(this);
		});
	};
})(jQuery, window, document);
//
/**
 * @module ussIsVisible
 *
 * @param  {String} visibleClass
 * @param  {String} notVisibleClass
 * @param  {String} _watch - next/prev/parent/specialTarget
 * @param  {jQDocument} - если параметр _watch = "specialTarget" => передать цель за которой следить
 */
$.fn.ussIsVisible = function(visibleClass, notVisibleClass, _watch, _watch_target) {
	var visibleClass = visibleClass || "inViewPort";
	var notVisibleClass = notVisibleClass || "notInViewPort";
	var target = this;
	var watch = _watch || "other";
	var targetToCheck;
	$.fn.isInViewport = function() {
		if (!this || this.length === 0) {
			return false;
		}
		var _this = this;
		if (typeof jQuery === "function" && _this instanceof jQuery) {
			_this = _this[0];
		}
		var rect = _this.getBoundingClientRect();
		return rect.top >= 0 && rect.left >= 0 && rect.bottom <= (window.innerHeight || document.documentElement.clientHeight) && rect.right <= (window.innerWidth || document.documentElement.clientWidth);
	};

	function init(e) {
		function isNotHidden(target) {
			if (target.css("display") === "none" || target.css("visibility") === "hidden" || target.css("opacity") === 0) {
				return false;
			} else {
				return true;
			}
		}
		var targets = {
			next: target.next("*"),
			prev: target.prev("*"),
			parent: target.parent("*"),
		};
		switch (watch) {
			case "next":
				targetToCheck = targets["next"];
				break;
			case "prev":
				targetToCheck = targets["prev"];
				break;
			case "parent":
				targetToCheck = targets["parent"];
				break;
			case "specialTarget":
				targetToCheck = _watch_target;
				break;
			default:
				targetToCheck = target;
				break;
		}
		if (isNotHidden(target)) {
			if (targetToCheck.isInViewport()) {
				target.removeClass(notVisibleClass).addClass(visibleClass);
			} else {
				target.removeClass(visibleClass).addClass(notVisibleClass);
			}
		} else {
			return;
		}
	}
	$(window).on("load scroll resize DOMContentLoaded", init);
	return this;
};
//
(function($, window, document, undefined) {
	$.fn.tableScoller = function() {
		return this.each(function(index, item) {
			var _this = $(item);
			if (_this.hasClass('noscroll')){
				return $(this);
			}
			var scrollBarParams = {
				axis: "x",
				theme: "dark",
			};
			var isParentValid = function(parent) {
				var ignoreParents = ["uss_shop_content2", "uss_section_content", "uss_shop_content", "inner", "uss_catalog_description", "tab_item"];
				var parentClassName = $(parent).attr("class");
				var isIgnore = false;
				for (var i = 0; i < ignoreParents.length; i++) {
					var classNameReg = new RegExp(ignoreParents[i], "gmi");
					if (classNameReg.test(parentClassName)) {
						isIgnore = true;
					}
				}
				if (isIgnore) {
					return null;
				} else {
					return parent;
				}
			};
			if ($().mCustomScrollbar) {
				var tableWrap = isParentValid($(_this).parent("div"));
				if (!tableWrap || tableWrap.length === 0) {
					$(_this).wrap('<div class="tableScoller-wrap"></div>');
					tableWrap = $(_this).parent(".tableScoller-wrap");
				}
				var table = $(_this);
				var tableWrapWidth = parseInt(tableWrap.css("width"));
				var tableTr = table.find("tr").eq(0);
				var tableTrWidth = parseInt(
					table
						.find("tr")
						.eq(0)
						.css("width")
				);
				if (tableTrWidth > tableWrapWidth) {
					tableWrap.addClass("uss-scroller");
					tableWrap.mCustomScrollbar(scrollBarParams);
				} else {
					tableWrap.removeClass("uss-scroller");
				}
			} else {
				console.warn("плагин mCustomScrollbar не подключен");
			}
			return $(this);
		});
	};
})(jQuery, window, document);
// EVENTS
//
var tableScollerFn = function() {
	$(".uss_shoppos_table").tableScoller();
	$(".uss_user_basket > table").tableScoller();
	$(".uss_section_content > table").tableScoller();
	$(".uss_shop_content2 > table").tableScoller();
	$(".uss_shop_content > table").tableScoller();
	$(".inner > table").tableScoller();
	$(".uss_catalog_description > table").tableScoller();
	$(".tab_item > table").tableScoller();
};

document.addEventListener("DOMContentLoaded", function(e) {
	queueRunner(e, [limit_slider_height, tableScollerFn, defineImageListSize]);
	$('div.uss_tabs .uss_tabs_navigation span').on('click',function (){
		$(".tab_item.selected > table").mCustomScrollbar("destroy");
		$(".tab_item.selected  table").unwrap();
		$(".tab_item.selected > table").tableScoller();
	});
});
$(window).on("resize", function(e) {
	queueRunner(e, [defineImageListSize]);
});
