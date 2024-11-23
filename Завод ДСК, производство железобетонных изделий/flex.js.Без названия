/*
	0. Детальный список правил, к которым применяется скрипт, можно найти в районе 78 строки.
	
	A. Виджет Template::displaySiteContent должен находиться в контейнере с классом "content". Других дополнительных указаний для системных выводов контента не требуется.
	
	B. Для виджетов у которых есть обертка для элементов требуется указать класс flex в качестве одно из родителей (ближайшего).
		есть обертка :
		- виджет каталога магазина
		- виджет каталога услуг
		- виджет фотоальбома
		- виджет меню фотоальбома
		- виджет вывода элементов каталог услуг в виде позиций

	C. Для всех остальных виджетов и своих блоков нужно использовать следующую структуру
		.flex
			.items
				.element
				.element
				.element
	Где .items является оберткой виджета или "своих" элементов , а .flex - общая обертка всего блока.
	* Дополнительные классы у flex и items никак не влияют на работу скрипта.
	
	** Для отмены работы скрипта предусмотрен класс noflex. Его можно использовать как для одного блока так и для всей страницы целиком. Для блоков типа  B и C нет смысла использовать класс noflex , проще убрать сам класс flex.

	Принцип работы скрипта: 
	
	1. Инициализация (Flex.detect_targets)
		a. - определяем все блоки в которых нужно посчитать отступы между элементами.
		b. - исключаем блоки у которых есть родитель с классом noflex
		c. - исключаем блоки если они являются слайдерами  'mCustomScrollbar', 'slick-slider'
		FIX 2019-05-22
			! Добавлена проверка вложенных класов для избежания повторного оборачивания .
			Если случилось так что виджет обернут в два или несколько родителей которые отслеживаются в пункте 1.а , то отступы будут считаться только для последнего (самого глубокого в дереве)
			Пример:
				- виджет обернут в 2 блока .items
				- .uss_widget_outer_content обернут в .items

	2. Для каждого блока из пункта 1 
		- добавляем класс uss-flex-items , для css стилей.
		- класс many-rows - элементы не влезают в 1 строку
		- класс one-row - все элементы влезли в 1 строку
		- класс one-in-row - 1 элемент в строку
		- класс last-in-row - последний элемент в строке
		- класс bigmargin - строк больше 1 & отступ между элементами больше 1/3 ширины блока 
		- добавляем уникальный класс items-*случайное число* для случаев когда выводиться 2 или несколько одинаковых виджетов с одним оформлением но разным содержанием.
			Пример: 2 виджета новинок с разнами размерами картинок.
	
	3. Для каждого блока считаем отступы  (Flex.calculate)
		- За базовую ширину элемента блока берем первый элемент в данном блоке , если он не является заголовком (для случаев когда новинки\лидеры\распродажа выводяться внутри контента магазина). Далее будем считать что ширина всех элементов одинаковая.
		- Считаем сколько блоков влезет в строку
			кол-во блоков больше 1 и строк больше одной: 
				- находим разность между длинной всех элементов в строке и шириной строки , полученный результат делим на кол-во отступов между элементами.
				- устанавливаем отступы через margin.
				- если bigmargin : центруем элементы внутри строки .
			кол-во блоков равно 1 : 
				- разность между шириной элемента и шириной сроки делим пополам и центруем элемент внутри строки за счет отступов.
					* Через стили можно задать свое повидение для такой ситуации. (.one-in-row)
			кол-во блоков большего одного и все в одной строке :
				- Считаем сколько может влезти элементов в строку => считаем отступы между каждым элементом если бы они заполняли всю строку целиком 
		
		* Если размер отступа меньше минимального размера (10) (Flex.min_element_margin) => уменьшаем кол-во элементов в строке на 1.

	4. слушаем событие resize и событие complete у jQuery.AJAX => запускаем пересчет заново. (Flex.resizer => Flex.calculate) 
		* complete у jQuery.AJAX для случаев когда включен параметр "При выборе фильтров, обновлять список товаров без перезагрузки страницы".
		* Для того чтоб пересчет на запускался слишком часто установленно ограничение на вызов функции пересчета. см. Flex.resizer 

	** Для получения определенного кол-ва элементов в строке с адаптивной шириной элемента задать в стилях для элемента:
		k = кол-во элементов в строке 
		10px = минимальный отступ между элементами
		width : calc( (100% - ( (k - 1) * 10px) ) / k )
*/
var Flex = {
    targets: [],
    wrap_classname: 'uss-flex-items',
    exclude_by_parent: ['noflex'],
    other_plugins: ['mCustomScrollbar', 'slick-slider'],
    avoid_double_wrap_classNames: '.items,.uss_widget_outer_content',
    min_element_margin: 10,
    target_with_parent: [
        '.flex .catalog_menu',
        '.flex .uss_eshop_menu',
        '.flex .items',
        '.content .uss_shop_blocks_view',
        '.content .uss_eshop_sameproducts:not(.table):not(.list)',
        '.content .uss_shop_block_cat.uss_shop_cats',
        '.flex .uss_images_block',
        '.content .uss_catalog_block_cat',
        '.flex.items',
        '.flex .lastEshopPosItems',
        '.uss_photoalbums_foto_box',
        '.flex .photoalbum_menu',
        '.content .uss_photoalbums_albums_block',
        '.flex .uss_catalog_sidebar.uss_catalog_list_cat',
        '.flex .uss_widget_outer_content',
        '.content .news_list.news_block_items',
        '.content .news_similar_wrap .similar_items.similar_items_block'
    ],
    target_with_out_parent: ['.flex .news_block_item'],
    get_random_class: function () {
        var num = Math.random() + '';
        num = num.replace('0.', '');
        return 'items-' + num;
    },
    detect_targets: function () {
        if (Flex.target_with_parent.length !== 0) {
            for (var i = 0; i < Flex.target_with_parent.length; i++) {
                _elem = $(Flex.target_with_parent[i]);
                if (_elem.children('*').length > 0 && !Flex.find_class_in_parents(Flex.target_with_parent[i])) {
                    for (var zi = 0; zi < _elem.length; zi++) {
                        //если эелемент уже обработан - пропускаем
                        if ($(_elem[zi]).hasClass('uss-flex-items')) {
                            continue;
                        }
                        if ($(_elem[zi]).find(Flex.avoid_double_wrap_classNames).length === 0) {
                            __elem = $(_elem[zi]);
                            r_class = Flex.get_random_class();
                            __elem.addClass(r_class).addClass(Flex.wrap_classname);
                            Flex.targets.push('.' + r_class);
                        }
                    }
                }
            }
        }
        if (Flex.target_with_out_parent.length !== 0) {
            $.each(Flex.target_with_out_parent, function (index, element) {
                if ($(element).length !== 0 && $(element).closest('.uss_widget_outer_content').length === 0) {
                    $(element).each(function (el_index, target) {
                        if (!$(target).parent('div').hasClass('items')) {
                            r_class = Flex.get_random_class();
                            _container = $('<div class="items"></div>');
                            $(target).siblings(element).appendTo(_container);
                            $(_container).insertAfter($(target));
                            $(target).prependTo(_container);
                            $(_container).addClass(r_class).addClass(Flex.wrap_classname);
                            Flex.targets.push('.' + r_class);
                        }
                    });
                }
            });
        }
    },
    remove_target: function (key) {
        var i = Flex.targets.indexOf(key);

        if (i !== -1) {
            delete Flex.targets[i];
        }
    },
    run: function () {
        if (Flex.targets.length !== 0) {
            for (var i = 0; i < Flex.targets.length; i++) {
                if (typeof Flex.targets[i] != 'undefined') {
                    Flex.calculate(Flex.targets[i]);
                }
            }
        }
    },
    resizer: function () {
        function throttle(func, ms) {
            var isThrottled = false,
                savedArgs,
                savedThis;

            function wrapper() {
                if (isThrottled) {
                    savedArgs = arguments;
                    savedThis = this;
                    return;
                }
                func.apply(this, arguments);
                isThrottled = true;
                setTimeout(function () {
                    isThrottled = false;
                    if (savedArgs) {
                        wrapper.apply(savedThis, savedArgs);
                        savedArgs = savedThis = null;
                    }
                }, ms);
            }
            return wrapper;
        }
        var onResize = function () {
            Flex.run();
        };
        var throttle_500 = throttle(onResize, 500);
        $(window).resize(function () {
            throttle_500();
        });
    },
    normalizeNumber: function (value, minimal = false, ignore) {
        let result = value;
        if (!ignore) {
            result = minimal ? Math.floor(value.toFixed(1)) : Math.ceil(value.toFixed(1));
        }
        return parseFloat(result);
    },

    calculate: function (target_parent) {
        box = $(target_parent);
        if (!box.length) {
            Flex.remove_target(target_parent);
            return false;
        }
        box_width = $(box)[0].getBoundingClientRect().width;
        box_width = Flex.normalizeNumber(box_width, true,true);
		
        elements = $(target_parent).children(
            '*:not(.uss_cleaner):not(.photo_page_navitem):not(.uss_shop_newbies_title):not(.h3)'
        );
        elements_lenght = $(target_parent).children(
            '*:not(.uss_cleaner):not(.photo_page_navitem):not(.uss_shop_newbies_title):not(.h3)'
        ).length;
        element_width = $(target_parent)
            .children('*:not(.uss_cleaner):not(.photo_page_navitem):not(.uss_shop_newbies_title):not(.h3)')[0]
            .getBoundingClientRect().width;
        element_width = Flex.normalizeNumber(element_width, false);
        max_elems_in_row = Flex.normalizeNumber(
            (box_width + Flex.min_element_margin) / (element_width + Flex.min_element_margin),
            true
        );
        box_width -= 1;
        elements_in_row = max_elems_in_row;

        function calcMargin(elemsRow) {
            return Flex.normalizeNumber((box_width - elemsRow * element_width) / (elemsRow - 1),true);
        }

        var margin_for_row = calcMargin(elements_in_row);

        var isOneRow = function () {
            // кол-во элементов меньше чем может поместиться в строку.
            return box_width - elements_lenght * Flex.min_element_margin >= elements_lenght * element_width
                ? true
                : false;
        };
        var big_margin = function () {
            return margin_for_row > element_width / 2 ? true : false;
        };
        var one_in_row = function () {
            return max_elems_in_row === 1 ? true : false;
        };

        var last_in_row = function () {
            elements = $(target_parent).children('*:not(.uss_cleaner):not(.photo_page_navitem)');
            var index = 0;
            var ii = 0;
            var makeZ = function (iterator, reset) {
                if (iterator === 0) {
                    ii = 0;
                } else {
                    ii = ii + 1;
                }
                if (reset) {
                    index = 1;
                    ii = 0;
                }
                z = ii - index;
                return z;
            };
            for (var i = 0; i < elements.length; i++) {
                _el = $($(elements)[i]);
                var z;
                if ($(_el).hasClass('uss_shop_newbies_title') || $(_el).hasClass('h3')) {
                    z = makeZ(i, true);
                } else {
                    z = makeZ(i);
                    if ((z + 1) % elements_in_row === 0) {
                        _el.css({
                            'margin-right': Flex.normalizeNumber(margin_right_last,true),
                            'margin-left': Flex.normalizeNumber(margin_left_last,true)
                        }).addClass('last-in-row');
                    } else {
                        _el.css({
                            'margin-right': Flex.normalizeNumber(margin_right,true),
                            'margin-left': Flex.normalizeNumber(margin_left,true)
                        });
                    }
                }
            }
        };
        var last_in_row_big_margin = function () {
            elements = $(target_parent).children('*:not(.uss_cleaner):not(.photo_page_navitem)');
            var index = 0;
            var ii = 0;
            var makeZ = function (iterator, reset) {
                if (iterator === 0) {
                    ii = 0;
                } else {
                    ii = ii + 1;
                }
                if (reset) {
                    index = 1;
                    ii = 0;
                }
                z = ii - index;
                return z;
            };
            for (var i = 0; i < elements.length; i++) {
                _el = $($(elements)[i]);
                var z;
                if ($(_el).hasClass('uss_shop_newbies_title')) {
                    z = makeZ(i, true);
                } else {
                    z = makeZ(i);
                    if ((z + 1) % elements_in_row === 0) {
                        _el.css({
                            'margin-right': Flex.normalizeNumber(margin_right_last, true),
                            'margin-left': Flex.normalizeNumber(margin_left_last, true)
                        });
                    } else if (z % elements_in_row === 0) {
                        _el.css({
                            'margin-right': 0,
                            'margin-left': Flex.normalizeNumber(margin_left, true)
                        }).addClass('last-in-row');
                    } else {
                        _el.css({
                            'margin-right': Flex.normalizeNumber(margin_right, true),
                            'margin-left': Flex.normalizeNumber(margin_left, true)
                        });
                    }
                }
            }
        };
        var last_elemenet_big_margin = function () {
            var _elements = $(elements);
            _elements.css({
                'margin-left': 0,
                'margin-right': Flex.normalizeNumber(margin_right, true)
            });
            _elements.eq(0).css({
                'margin-left': Flex.normalizeNumber(margin_left, true),
                'margin-right': Flex.normalizeNumber(margin_right, true)
            });
        };
        var margin_left, margin_right, margin_left_last, margin_right_last;
        $(box).removeClass('bigmargin');
        $(box).removeClass('one-row');
        $(box).removeClass('many-rows');
        $(box).removeClass('one-in-row');

        if (isOneRow()) {
            $(target_parent).addClass('one-row');
            // все элементы влезают в 1 строку
            margin_for_row = Flex.normalizeNumber(
                (box_width - elements_lenght * element_width) / (elements_lenght - 1),
                true
            );
            if (one_in_row()) {
                $(box).addClass('one-in-row');
                margin_for_row = ~~(box_width - element_width);
                margin_left = margin_for_row / 2;
                margin_right = margin_for_row / 2;
                margin_left_last = margin_for_row / 2;
                margin_right_last = margin_for_row / 2;
                last_in_row();
            } else {
                margin_left = 0;
                margin_right = margin_for_row;
                margin_left_last = 0;
                margin_right_last = 0;
                elements_in_row = elements_lenght;
                last_in_row();
                if (big_margin()) {
                    margin_for_row = Flex.normalizeNumber(
                        (box_width - max_elems_in_row * element_width) / (max_elems_in_row + 1),
                        true
                    );
                    margin_left = 0;
                    margin_right = margin_for_row;
                    margin_left_last = 0;
                    margin_right_last = margin_for_row;
                    last_elemenet_big_margin();
                }
            }
        } else {
            // все элементы НЕ влезают в 1 строку
            $(target_parent).addClass('many-rows');
            if (one_in_row()) {
                $(box).addClass('one-in-row');
                margin_for_row = Flex.normalizeNumber(box_width - element_width, true);
                margin_left = margin_for_row / 2;
                margin_right = margin_for_row / 2;
                margin_left_last = margin_for_row / 2;
                margin_right_last = margin_for_row / 2;
                last_in_row();
            } else if (big_margin()) {
                $(box).addClass('bigmargin');
                margin_fix = 11 * (elements_in_row + 1);
                margin_for_row = Flex.normalizeNumber(
                    (box_width - elements_in_row * element_width) / (elements_in_row + 1),
                    true
                );
                margin_left = margin_for_row;
                margin_right = 0;
                margin_left_last = margin_for_row;
                margin_right_last = margin_for_row;
                if (max_elems_in_row > 2) {
                    last_in_row_big_margin();
                } else {
                    last_in_row();
                }
            } else {
                margin_left = 0;
                margin_right = margin_for_row;
                margin_left_last = 0;
                margin_right_last = 0;
                last_in_row();
            }
        }
    },
    find_class_in_parents: function (target) {
        find = false;
        $.each($(target).parents(), function (index, val) {
            $.each(Flex.exclude_by_parent, function (index2, val2) {
                if ($(val).hasClass(val2)) {
                    find = true;
                }
            });
        });
        return find;
    },
    init: function (type) {
        Flex.exclude_by_parent = Flex.exclude_by_parent.concat(Flex.other_plugins);
        Flex.detect_targets();
        Flex.run();
        Flex.resizer();
    }
};
jQuery(document).ready(function ($) {
    $(window).load(function () {
        Flex.init();
    });
    $.ajaxSetup({
        complete: function () {
            Flex.init();
        }
    });
});
// *** iconsCalculate ***
// У блоков обязательно должен быть обший родитель
// Стандартный вызов
// $('.icons .icon').iconsCalculate();
// Вызов с кастомной схемой распределения
// $('.icons .icon').iconsCalculate({"1920":[2,3,5],"1200":[3,3,3,1],"768":[2,2,2,2,2]});
(function ($, window, document, undefined) {
    function findResolution(_window_width, array) {
        var array_keys = [];
        for (key in array) {
            array_keys.push(parseInt(key));
        }
        array_keys = array_keys.sort(function (a, b) {
            return a - b;
        });
        for (var i = array_keys.length - 1; i >= 0; i--) {
            if (_window_width <= array_keys[i] && _window_width > (array_keys[i - 1] || 0)) {
                return array_keys[i];
            }
        }
        return array_keys[array_keys.length - 1];
    }

    function buildRows(schema, resolution, _items, parent) {
        for (var i = 0; i < schema[resolution].length; i++) {
            var j = schema[resolution][i];
            row = _items.splice(0, j);
            $(row).wrapAll('<div class="row x' + j + '"></div>');
        }
        $(parent).attr('resolution', resolution);
    }
    $.fn.iconsCalculate = function (optional_schema) {
        var _this = this;
        function init() {
            var items = _this;
            var parent;
            _this
                .eq(0)
                .parents('div')
                .each(function (index, el) {
                    if (!$(el).hasClass('row') && parent === undefined) {
                        parent = $(el);
                    }
                });
            var items_length = items.length.toString();
            var default_schema = {
                // стандартная схема распределения
                8: {
                    1920: [4, 4],
                    1200: [3, 3, 2],
                    768: [2, 2, 2, 2],
                    550: [1, 1, 1, 1, 1, 1, 1, 1]
                },
                7: {
                    1920: [4, 3],
                    1200: [3, 3, 1],
                    768: [2, 2, 2, 1],
                    550: [1, 1, 1, 1, 1, 1, 1]
                },
                6: {
                    1920: [4, 2],
                    1200: [3, 3],
                    768: [2, 2, 2],
                    550: [1, 1, 1, 1, 1, 1]
                },
                5: {
                    1920: [3, 2],
                    768: [2, 2, 1],
                    550: [1, 1, 1, 1, 1]
                },
                4: {
                    1920: [4],
                    768: [2, 2],
                    550: [1, 1, 1, 1, 1]
                },
                3: {
                    1920: [3],
                    768: [2, 1],
                    550: [1, 1, 1]
                },
                2: {
                    1920: [2],
                    768: [1, 1]
                },
                1: {
                    1920: [1]
                },
                0: {
                    2000: [100]
                }
            };
            var activeSchema = optional_schema || default_schema[items_length];
            if ($(parent).attr('resolution') === undefined) {
                $(parent).attr('resolution', '0');
            }
            var resolution = findResolution($(window).width(), activeSchema);
            if ($(parent).attr('resolution') === '0') {
                buildRows(activeSchema, resolution, items, parent);
            } else if (parent.attr('resolution') !== '0' && parent.attr('resolution') !== resolution) {
                items.unwrap('.row');
                buildRows(activeSchema, resolution, items, parent);
            }
        }
        if (this.length > 0) {
            init();
        }
        return _this;
    };
})(jQuery, window, document);
