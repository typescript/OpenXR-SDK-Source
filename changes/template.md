{% macro format_ref(ref) -%}
{%- set service = ref.service_params[0] -%}
{%- if service == "gh" -%}
    {%- set project = ref.service_params[1] %}
    {%- set project_base = "https://github.com/KhronosGroup/" + project %}
    {%- set link_text %}{{ project }}/#{{ ref.number }}{% endset %}
    {%- if ref.item_type == "issue" -%}
        {%- set subdir = "issues" %}
    {%- else -%}
        {%- set subdir = "pull" %}
    {%- endif -%}
{%- else -%}
    {%- set project_base = "https://gitlab.khronos.org/openxr/openxr" %}
    {%- if ref.item_type == "issue" -%}
        {%- set link_text %}internal issue {{ ref.number }}{% endset %}
        {%- set subdir = "issues" %}
    {%- else -%}
        {%- set link_text %}internal MR {{ ref.number }}{% endset %}
        {%- set subdir = "merge_requests" %}
    {%- endif -%}
{%- endif -%}
[{{ link_text }}]({{project_base}}/{{subdir}}/{{ ref.number }})
{%- endmacro -%}
{% macro format_refs(refs) -%}
    {% if (refs | length) > 0 %}
        {%- set comma = joiner(", ") -%}
        {% for ref in refs -%}
            {{comma()}}{{format_ref(ref)}}
        {%- endfor %}
    {%- endif %}
{%- endmacro -%}
{% block title %}## {{ project_name }} {{project_version}} ({{date}}){% endblock %}
{% block sections_and_fragments -%}
{%- for section in sections %}
- {{ section.name }}
{%- for fragment in section.fragments %}
  - {% set rawtext %}{{ fragment.text }} ({{format_refs(fragment.refs)}}){% endset %}{{ rawtext | wordwrap | indent }}
{%- else %}
  - No significant changes
{%- endfor -%}
{%- endfor %}{% endblock %}
